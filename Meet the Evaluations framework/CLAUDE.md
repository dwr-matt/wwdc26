# Meet the Evaluations Framework — Session Notes

Code in this file is sourced from official documentation fetched via JSON API:
- https://developer.apple.com/tutorials/data/documentation/evaluations/evaluating-language-model-responses.json
- https://developer.apple.com/tutorials/data/documentation/evaluations/designing-effective-evaluations.json
- https://developer.apple.com/tutorials/data/documentation/evaluations/designing-evaluation-datasets.json

---

## Why This Framework Exists

Traditional unit tests assume "same input = same output." AI models are **stochastic** — that contract doesn't hold. The Evaluations Framework measures:
- How often does the model produce unexpected results?
- How often does an agent take the wrong path?
- Under what circumstances does it produce unsafe output?

---

## Five Steps to Build an Evaluation

| Step | What |
|---|---|
| **1. Subject** | The AI feature being tested |
| **2. Dataset** | Input samples + expected outputs |
| **3. Evaluators / Metrics** | How each sample is scored |
| **4. Aggregate** | Roll up per-sample scores into statistics |
| **5. Test** | Run in Xcode test target, assert against threshold |

---

## Code: Full Evaluation Example

### Define the Evaluation

```swift
import Evaluations
import FoundationModels

struct LetterCountEvaluation: Evaluation {
    let dataset = ArrayLoader(samples: [
        ModelSample(prompt: "Count the letter 'r' in 'strawberry'.", expected: 3),
        ModelSample(prompt: "How many a's are in 'banana'?", expected: 3),
        ModelSample(prompt: "Mississippi contains how many s?", expected: 4),
        ModelSample(prompt: "What's the number of l in hello?", expected: 2),
        ModelSample(prompt: "The letter 'e' in 'bookkeeper' appears how many times?", expected: 3),
    ])
}
```

### Define Subject (the AI feature)

```swift
func subject(from sample: ModelSample<Int>) async throws -> ModelSubject<Int> {
    let session = LanguageModelSession()
    let response = try await session.respond(to: sample.prompt, generating: Int.self)
    return ModelSubject(
        value: response.content,
        transcript: session.transcript.structuredTranscript
    )
}
```

### Define Evaluators (quantitative metrics)

```swift
let exactMatch = Metric("ExactMatch")
let absoluteError = Metric("AbsoluteError")

var evaluators: Evaluators {
    Evaluator { input, subject in
        guard let expected = input.expected else { return exactMatch.ignore() }
        return subject.value == expected ? exactMatch.passing() : exactMatch.failing()
    }
    Evaluator { input, subject in
        guard let expected = input.expected else { return absoluteError.ignore() }
        let error = abs(Double(subject.value - expected))
        return absoluteError.scoring(error)
    }
}
```

Metric return values:
- `.passing(rationale:)` — binary pass
- `.failing(rationale:)` — binary fail
- `.scoring(_ value:rationale:)` — numeric score
- `.ignore()` — skip this sample

### Aggregate Results

```swift
func aggregateMetrics(using aggregator: inout MetricsAggregator) {
    aggregator.computeMean(of: exactMatch)
    aggregator.computeMean(of: absoluteError)
}

// Or grouped:
func aggregateMetrics(using aggregator: inout MetricsAggregator) {
    aggregator.group("Accuracy") { group in
        group.computeMean(of: exactMatch)
    }
    aggregator.group("Error") { group in
        group.computeMaximum(of: absoluteError)
    }
}
```

### Run in Swift Testing

```swift
import Testing
import Evaluations

struct LetterCountTests {
    static let evaluation = LetterCountEvaluation()

    @Test(.evaluates(Self.evaluation))  // new test trait
    func letterCounting() async throws {
        let result = EvaluationContext.current.result
        let score = result.aggregateValue(.mean(of: Self.evaluation.exactMatch))
        #expect(score > 0.8)  // 80% pass rate threshold
    }
}
```

### Inspect Detailed Results

```swift
@Test(.evaluates(Self.evaluation))
func inspectDetailedResults() async throws {
    let result = EvaluationContext.current.result

    let inputs   = result.detailed[Self.evaluation.inputColumn]
    let expected = result.detailed[Self.evaluation.expectedColumn]
    let scores   = result.detailed[metric: Self.evaluation.exactMatch]

    for row in 0..<scores.count where scores[row]?.value == .failing {
        let prompt = inputs[row]?.promptDescription ?? "<missing>"
        let target = expected[row].map(String.init) ?? "?"
        print("Missed (expected \(target)): \(prompt)")
    }
}
```

---

## Quantitative vs Qualitative — Rule of Thumb

- Can you measure it in code? → **Quantitative** (`Evaluator`)
- Can you only describe it in words? → **Qualitative** (`ModelJudgeEvaluator`)

---

## Model Judge (Qualitative Metrics)

Use a more capable model to score your AI feature's output at scale.

```swift
var evaluators: Evaluators {
    ModelJudgeEvaluator(
        "HaikuQuality",
        scale: .numeric([
            4: "Excellent - Perfect 5-7-5 form, strongly relevant, vivid imagery.",
            3: "Good - Correct or near-correct form, clearly relevant.",
            2: "Poor - Incorrect syllable count, weak connection to topic.",
            1: "Very poor - Not recognizable as a haiku, off-topic, or incoherent.",
        ]),
        judge: SystemLanguageModel.default,  // or use PCC model
        prompt: ModelJudgePrompt(
            instructions: """
                You are an expert poetry evaluator. Evaluate quality considering:
                form, relevance, and imagery. Give step-by-step explanations.
                """
        )
    )
}
```

Key design rules:
- Use an **even-numbered scale** (e.g., 1–4) — prevents the judge defaulting to a neutral middle
- Always require **rationale** — essential for diagnosing why something failed
- Judge should be at least as capable as the model under test (typically use PCC to judge on-device)

---

## Score Dimensions — Splitting Broad Metrics

If judge scores are vague or you disagree with them, the question is too broad. Split it:

```swift
// "Quality" is too broad → split into Relevance + Usefulness
// Each dimension gets its own description so the judge understands
// the specific meaning in your app's context

var evaluators: Evaluators {
    ModelJudgeEvaluator("Relevance", scale: ..., judge: ..., prompt: ...)
    ModelJudgeEvaluator("Usefulness", scale: ..., judge: ..., prompt: ...)
}
```

Add app context to the judge prompt so it understands your domain (e.g., "You are evaluating tags for a personal library app, not a review platform").

---

## Dataset Design

Organize into four suites:

```swift
@Suite("Golden") struct GoldenEvaluations { }           // core happy path
@Suite("Edge cases") struct EdgeCaseEvaluations { }     // boundary behavior
@Suite("Adversarial") struct AdversarialEvaluations { } // prompt injection, safety bypasses
@Suite("Known failures") struct KnownFailureEvaluations { } // regression tests
```

---

## SampleGenerator — Synthetic Dataset Expansion

Start with 20–30 hand-curated samples, then expand synthetically:

```swift
let workSeeds = dataset.filter { $0.expected?.category == .work }
let workPrompt = Prompt("""
    Generate realistic work tasks with explicit deadlines within 48 hours.
    Vary phrasing: some as direct requests, some as meeting invites, some as
    forwarded messages. Each needs a clear urgency signal.
    """)
for try await sample in workSeeds.makeSamples(workPrompt, targetCount: 30) {
    expanded.append(sample)
}
```

Use focused prompts per category — not a single "generate diverse examples" prompt.

---

## Tool-Calling Evaluation

Evaluate whether an agent calls the right tools with the right arguments:

```swift
ModelSample(
    prompt: "Count the letter 'r' in 'strawberry'.",
    expected: 3,
    expectations: TrajectoryExpectation(
        ordered: [
            ToolExpectation(
                "count_letters",
                arguments: [
                    .exact(argumentName: "letter", value: .string("r")),
                    .exact(argumentName: "word", value: .string("strawberry")),
                ]
            ),
        ]
    )
)

let toolsAllPass = Metric("ToolsAllPass")
let toolsPercentagePass = Metric("ToolsPercentagePass")

var evaluators: Evaluators {
    ToolCallEvaluator(allPass: toolsAllPass, percentagePass: toolsPercentagePass)
}
```

---

## Hill Climbing Loop

```
20–30 hand-curated samples
    ↓
Run evaluation
    ↓
Review Xcode Evaluation Report (visual dashboard)
    ↓
Read rationales → find failure patterns
    ↓
Update prompt / instructions
    ↓
Re-run → confirm improvement, no regression
    ↓ (repeat until target met)
```

Centering your development process on this loop = **Evaluation-Driven Development**.

---

## Xcode Integration

- **Evaluation Report**: Report Navigator → Evaluations
- Compare across runs to track prompt changes
- `notes` dictionary on the evaluation run records the config for comparison

---

## Related Sessions
- *Improve your prompts by hill climbing with Evaluations* — deep dive on iteration strategy
- *Create robust evaluations for agentic apps* — advanced `ModelSample` and tool-calling evaluation

---

## Resources
- [Evaluating language model responses](https://developer.apple.com/documentation/Evaluations/evaluating-language-model-responses)
- [Designing effective evaluations](https://developer.apple.com/documentation/Evaluations/designing-effective-evaluations)
- [Designing datasets to test your feature](https://developer.apple.com/documentation/Evaluations/designing-evaluation-datasets)
- [Book Tracker sample app](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- Session page: https://developer.apple.com/videos/play/wwdc2026/298/
