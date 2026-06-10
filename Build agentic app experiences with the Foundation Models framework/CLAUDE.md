# Build Agentic App Experiences with the Foundation Models Framework — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/composing-dynamic-sessions-with-instructions-and-profiles.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/expanding-generation-with-tool-calling.json

---

## What Is a DynamicProfile?

A declarative way to define the **configuration state** of a language model session. The body is re-evaluated on every prompt — change a variable and the session's persona switches automatically, no new session needed.

---

## DynamicInstructions

Groups instructions and tools into a reusable, composable component. Nesting concatenates instructions and tools.

```swift
struct PresentationInstructions: DynamicInstructions {
    var isEditingImage = true
    var isEditingAnimation = false

    var body: some DynamicInstructions {
        Instructions {
            "Help people improve their presentation."
        }
        ListPhotosTool()
        AddPhotoTool()

        if isEditingImage {
            ImageEditingInstructions()
        }

        if isEditingAnimation {
            AnimationEditingInstructions()
        }
    }
}

// Use with a session directly
let session = LanguageModelSession(
    dynamicInstructions: PresentationInstructions()
)
```

---

## DynamicProfile

Full agent configuration: instructions, tools, model, and modifiers.

```swift
struct PresentationProfile: LanguageModelSession.DynamicProfile {
    var pccModel = PrivateCloudComputeLanguageModel()

    var isEditingImage = true
    var isEditingAnimation = false

    var body: some LanguageModelSession.DynamicProfile {
        if isEditingImage {
            Profile {
                ImageEditingInstructions()
            }
        } else if isEditingAnimation {
            Profile {
                AnimationEditingInstructions()
            }
            .model(pccModel)
            .temperature(0.2)
            .reasoningLevel(.light)
        } else {
            Profile {
                PresentationDynamicInstructions()
            }
            .temperature(0.8)
        }
    }
}

// Use with a session
let session = LanguageModelSession(profile: PresentationProfile())
```

Inline modifier syntax:

```swift
Profile {
    // Custom instructions and tools.
}
.model(pccModel)
.temperature(likesPoetry ? 0.8 : 0.1)
.reasoningLevel(likesAstronomy ? .deep : .light)
```

---

## Lifecycle Modifiers

Run imperative code at specific points in the session lifecycle.

Available methods:
- `onActivate(perform:)` — profile becomes active
- `onDeactivate(perform:)` — profile becomes inactive
- `onPrompt(perform:)` — after user prompt, before model request
- `onResponse(perform:)` — after model produces a response
- `onToolCall(perform:)` — when model invokes a tool
- `onToolOutput(perform:)` — when tool call produces output

```swift
Profile {
    MyCustomFileAccessInstructions()
    MyCustomReadFileTool()
}
.onToolCall { toolCall in
    guard myAccessPolicy.permits(toolCall) else {
        throw MyAccessPolicyError.denied(toolCall.toolName)
    }
}
.onToolOutput { toolCall, output in
    // Log activity after tool execution
}
```

---

## Session Properties

Shared state accessible from any profile or tool across the session.

Built-in `history` property:

```swift
@SessionProperty(\.history)
var history

var body: some LanguageModelSession.DynamicProfile {
    Profile {
        Instructions("You are a helpful assistant.")
        TodoWriteTool()
    }
    .onResponse {
        if history.count > 100 {
            history = history.suffix(50)
        }
    }
}
```

Custom session properties:

```swift
extension SessionPropertyValues {
    @SessionPropertyEntry
    var activatedSkills: [String: Bool] = [:]
}

struct PlannerTool: Tool {
    let description = "Update the state of the activated skills"

    @SessionProperty(\.activatedSkills)
    var activatedSkills

    @Generable
    struct Arguments {
        @Guide(description: "The skills to activate")
        var skills: [String]
    }

    func call(arguments: Arguments) -> String {
        for skill in arguments.skills {
            activatedSkills[skill] = true
        }
        return "Activated: \(arguments.skills.joined(separator: ", "))"
    }
}
```

> `history` property changes are **lossy and global** — affect all profiles. For lossless, profile-specific trimming, use `historyTransform` instead.

---

## History Transform

Applied only before prompting this profile. Does not permanently mutate the transcript.

```swift
Profile {
    Instructions("You help people generate fun and interesting book ideas.")
    MyCustomBookTool()
}
.historyTransform { history in
    Array(history.suffix(20))
}
```

---

## Custom Profile Modifier

```swift
struct DebugProfileModifier: LanguageModelSession.DynamicProfileModifier {
    func body(content: Content) -> some LanguageModelSession.DynamicProfile {
        content
            .temperature(0.0)
            .onResponse { response in
                print("Debug response: \(response)")
            }
    }
}

extension LanguageModelSession.DynamicProfile {
    func debug() -> some LanguageModelSession.DynamicProfile {
        self.modifier(DebugProfileModifier())
    }
}

// Usage
Profile {
    Instructions("You are a helpful assistant.")
}
.debug()
```

---

## Tool Calling Mode (via DynamicProfile)

Control when tool calls happen using a dynamic property:

```swift
extension SessionPropertyValues {
    @SessionPropertyEntry
    var toolCallCount: Int = 0
}

struct RecipeDynamicProfile: LanguageModelSession.DynamicProfile {
    @SessionProperty(\.toolCallCount)
    var toolCallCount

    var body: some LanguageModelSession.DynamicProfile {
        Profile {
            BreadDatabaseTool()
        }
        .toolCallingMode(toolCallCount < 1 ? .required : .allowed)
        .onToolCall {
            toolCallCount += 1
        }
    }
}
```

Modes: `.allowed` (default), `.required` (forces tool call — acts like a while loop, must have exit condition), `.disallowed`

---

## Error Handling Policy

```swift
Profile {
    // Your dynamic instructions and tools.
}
.temperature(0.9)
.transcriptErrorHandlingPolicy(.preserveTranscript)
```

Options: `.revertTranscript` (default — auto rollback on error), `.preserveTranscript` (manual control — you must restore valid state before next prompt)

---

## Modifier Precedence

1. **Highest:** Arguments passed to `respond(to:options:)` at call site
2. **Middle:** Innermost profile or profile modifier
3. **Lowest:** Outer dynamic profile modifiers (act as defaults)

---

## Orchestration Patterns

### BatonPass (Collaboration)
- Profiles share the full transcript
- A tool call changes the active profile variable
- Receiving profile sees full history and delivers the final answer

### Phone-a-Friend (Consultation)
- A tool spawns an isolated child session with its own transcript
- Child completes its task and returns result as tool output
- Child session is discarded; parent delivers the final answer

Both patterns are described verbally in the transcript — no verified code available beyond the primitives shown above.

---

## KV Cache Performance

Modifying the transcript invalidates the KV cache and increases latency:

| Operation | Cache |
|---|---|
| Append to transcript | ✅ preserved |
| Remove entries | ❌ invalidated |
| Change instructions | ❌ invalidated |
| Change tools | ❌ invalidated |

Use the Foundation Models Instrument in Xcode to detect cache invalidations. Use the Evaluations framework to verify accuracy after transcript mutations.

---

## Related Sessions
- [What's new in the Foundation Models framework](../What%27s%20new%20in%20the%20Foundation%20Models%20framework/) — overview
- [Build with the new Apple Foundation Model on Private Cloud Compute](../Build%20with%20the%20new%20Apple%20Foundation%20Model%20on%20Private%20Cloud%20Compute/)
- Meet the Evaluations Framework

---

## Resources
- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [Expanding generation with tool calling](https://developer.apple.com/documentation/FoundationModels/expanding-generation-with-tool-calling)
- Session page: https://developer.apple.com/videos/play/wwdc2026/242/
