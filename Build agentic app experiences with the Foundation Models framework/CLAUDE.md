# Dynamic Profiles — Session Notes

## Why It Exists

Two core problems when building agentic apps with Foundation Models:

1. **Context management** — long conversations exceed the model's context window
2. **Model boundaries** — different tasks need different models (cost vs. capability)

`DynamicProfile` solves both.

---

## What Is a DynamicProfile?

A declarative way to define the **configuration state** of a language model session. Each profile represents one "agent persona" — swap a variable and the session's identity changes instantly without creating a new session.

A profile has four components:

| Component | Purpose |
|---|---|
| **Instructions** | The model's goal and role |
| **Tools** | Capabilities available to this agent |
| **Modifiers** | Model selection, temperature, reasoning level, etc. |
| **Dynamic Instructions** | Reusable, composable blocks of instructions + tools |

---

## Code Structure

```swift
// Reusable expert component
struct OrigamiExpert: DynamicInstructions { ... }

@DynamicProfile
var brainstorming: some DynamicProfile {
    BrainstormFacilitator()
    if isOrigamiProject { OrigamiExpert() }
    GenerateTitleTool()
    Model(.privateCloudCompute)
    Temperature(1.0)
}

@DynamicProfile
var planning: some DynamicProfile {
    Model(.privateCloudCompute)
    ReasoningLevel(.deep)
}

@DynamicProfile
var reviewing: some DynamicProfile {
    Model(.system)          // cheaper on-device model
    .dropToolCallHistory()
}

// Usage: changing `mode` switches the active agent
let session = LanguageModelSession(profile: { mode })
```

> Profile body is **re-evaluated on every prompt**, so changing `mode` automatically switches personas.

---

## Dynamic Instructions

Bundles instructions + tools into a reusable component. Nesting is supported — nested components concatenate their instructions and tools automatically.

---

## Transcript Management

Two approaches when trimming context between model switches:

**HistoryTransform** (lossless, profile-scoped — prefer this)
```swift
HistoryTransform { history in
    history.filter { !$0.isToolCall }
}
```
Applied only before prompting this profile. Does not permanently mutate the transcript.

**History Property** (lossy, global)
```swift
.onResponse { session in
    session.properties.history = summarize(session.properties.history)
}
```
Permanently modifies the transcript. Visible to all profiles.

---

## Session Properties

Shared state accessible from any profile or tool:

```swift
extension SessionPropertyValues {
    @SessionPropertyEntry var conversationSummary: String? = nil
}
```

---

## Orchestration Patterns

**BatonPass (collaboration)**
- Profiles share the full transcript
- A tool call changes the active profile variable
- The receiving profile sees full history and delivers the final answer

**Phone-a-Friend (consultation)**
- A tool spawns an isolated child session
- Child completes its task and returns the result as tool output
- Child session is discarded; parent delivers the final answer

---

## Tool Calling Mode

```swift
.allowed      // default — model decides whether to call a tool
.disallowed   // prevent tool calls when they're irrelevant
.required     // force tool calls — acts like a while loop, must have an exit condition
```

---

## Performance: KV Cache

| Operation | Cache |
|---|---|
| Append to transcript | ✅ preserved |
| Remove entries | ❌ invalidated |
| Change instructions | ❌ invalidated |
| Change tools | ❌ invalidated |

Use the **Foundation Models Instrument** in Xcode to detect cache invalidations. Use the **Evaluations framework** to verify accuracy after transcript mutations.

---

## Resources

- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- Session page: https://developer.apple.com/videos/play/wwdc2026/242/
