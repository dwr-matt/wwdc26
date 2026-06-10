# Improve your prompts by hill-climbing with Evaluations — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/evaluations/designing-effective-model-judges.json
- https://developer.apple.com/tutorials/data/documentation/evaluations/designing-evaluation-criteria.json

---

## What Is Hill Climbing?

A systematic iterative optimization loop treating AI feature development as controlled experiments:

```
Build baseline evaluation → Run → Find failure patterns → Make ONE change → Re-run → Compare scores → Repeat
```

**Critical rule**: Only change one variable at a time (one prompt instruction, one tool) so score changes are attributable to that specific change.

---

## Three Levels of Improvement

### 1. Prompt Improvements

**Add app context**
Tell the judge your app's purpose — e.g., "You are evaluating tags for a personal library app, not a review platform." Prevents the judge from applying generic criteria that don't match your users.

**Add few-shot examples (positive + negative)**
Give the judge concrete good/bad examples to clarify the scoring rubric. WARNING: do not provide an exhaustive list — overfitting causes the judge to fail on inputs outside your dataset.

**Refine scoring rubrics**
Replace vague descriptions with observable feature descriptions:

```swift
let haikuScale = ScoringScale.numeric([
    4: "Perfect 5-7-5 form, strongly relevant to the topic, uses vivid imagery.",
    3: "Correct or near-correct form, clearly relevant, some evocative language.",
    2: "Incorrect syllable count, weak topic connection, or lacks poetic quality.",
    1: "Not recognizable as a haiku, off-topic, or incoherent.",
])
```

Rubric design rules:
- Use **even-numbered scales** (e.g., 1–4) to force commitment, prevent defaulting to neutral
- Describe **observable features**, not quality judgments
- Make adjacent levels distinguishable
- Anchor all levels equally

### 2. Split Score Dimensions

"Quality" is too broad. Split into separate `ModelJudgeEvaluator` instances:

```swift
var evaluators: Evaluators {
    ModelJudgeEvaluator("Relevance", scale: relevanceScale, judge: ..., prompt: ...)
    ModelJudgeEvaluator("Usefulness", scale: usefulnessScale, judge: ..., prompt: ...)
}
```

When Relevance improves but Usefulness drops, you know exactly where to focus next.

### 3. Feature-Level Improvement (Add Tools)

When prompt iteration plateaus, add tools. Use Comparative Evaluation to compare baseline (no tool) vs. experimental (with tool) side-by-side in Xcode's Evaluation Report.

---

## Scoring Scale API

```swift
// Binary scale
let safetyScale = ScoringScale.passFail(
    passDescription: "Output is safe and on-topic.",
    failDescription: "Output contains unsafe content or goes off-topic."
)

// Numeric scale (use even numbers)
let qualityScale = ScoringScale.numeric([
    4: "...",
    3: "...",
    2: "...",
    1: "...",
])
```

---

## Code-Based vs. Model-Judge Evaluators

```swift
// Quantitative (computable) → Evaluator
Evaluator { input, subject in
    subject.value.split(separator: " ").count <= 200
        ? wordLimit.passing() : wordLimit.failing()
}

// Qualitative (requires reasoning) → ModelJudgeEvaluator
ModelJudgeEvaluator("Relevance", scale: .passFail(...), judge: SystemLanguageModel.default, prompt: ...)
```

| Type | Speed | Cost | Reproducibility |
|---|---|---|---|
| `Evaluator` | Instant | Free | Perfect |
| `ModelJudgeEvaluator` | Seconds | Inference cost | High (with well-defined rubrics) |

---

## Drift: The Judge Alignment Problem

Over time, the model judge's ratings may diverge from human expert standards — this is **drift**.

### Solution: Cohen's Kappa Coefficient

Periodically build an alignment evaluation using hand-labeled samples and compare against judge scores:

```
Accuracy    = % of scores that exactly match
Coincidence = probability of random agreement (from score distribution)
Kappa       = (Accuracy - Coincidence) / (1 - Coincidence)
```

- Kappa ≥ 0.8 → good alignment
- Kappa < 0.6 → judge has drifted, recalibrate

No verified code for the alignment evaluation — described verbally in transcript.

---

## Comparative Evaluation

Run two versions (control baseline + experimental) in the same test suite. Xcode's Evaluation Report provides side-by-side comparison to see exactly which samples improved or regressed.

Access: **Report Navigator → Evaluations**

---

## Judge Bias Mitigation

| Bias | Symptom | Mitigation |
|---|---|---|
| Verbosity bias | Prefers longer answers | Add conciseness as an explicit scoring dimension |
| Leniency bias | Skews toward high scores | Use even-numbered scale to force differentiation |
| Self-enhancement bias | Favors output from the same model | Use a different model as judge (e.g., PCC to judge on-device) |

---

## Key Principles

1. Change one variable at a time — isolate causation
2. Periodically calculate Kappa — verify judge hasn't drifted
3. Failed experiments have value — they show where to focus next
4. Overfitting risk: don't give the judge an exhaustive example list
5. Goal: **Evaluation-Driven Development** — evaluation scores are the core feedback loop

---

## Related Sessions
- [Meet the Evaluations framework](../Meet%20the%20Evaluations%20framework/) — framework fundamentals
- *Create robust evaluations for agentic apps* — Tool Call Evaluators, Sample Generator API

---

## Resources
- [Book Tracker sample app](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Designing effective model-as-judge evaluators](https://developer.apple.com/documentation/Evaluations/designing-effective-model-judges)
- [Designing specific, measurable criteria](https://developer.apple.com/documentation/Evaluations/designing-evaluation-criteria)
- Session page: https://developer.apple.com/videos/play/wwdc2026/335/
