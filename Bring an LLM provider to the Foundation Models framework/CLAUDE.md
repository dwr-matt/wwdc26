# Bring an LLM Provider to the Foundation Models Framework — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/languagemodel.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/languagemodelexecutor.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/languagemodelexecutorgenerationchannel.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/languagemodelcapabilities.json

---

## What This Session Is About

Apple opens Foundation Models framework to third-party providers. Any model (Anthropic, Google, local weights) can conform to a Swift protocol and plug into the entire ecosystem — Dynamic Profiles, Tool Calling, Structured Output, Multimodal. Providers distribute via Swift Package Manager. From the app developer's side: **swap one line of code to change the underlying model**.

---

## Two Core Protocols

### 1. `LanguageModel` (lightweight configuration)

```swift
struct MyCustomServerLanguageModel: LanguageModel {
    var capabilities: LanguageModelCapabilities {
        LanguageModelCapabilities(capabilities: [
            .toolCalling,
            .guidedGeneration,
            .reasoning
        ])
    }

    var executorConfiguration: MyExecutor.Configuration { ... }

    associatedtype Executor: LanguageModelExecutor
}

// App usage is identical regardless of underlying model:
let session = LanguageModelSession(model: MyCustomServerLanguageModel())
let response = try await session.respond(to: "Tell me a joke!")
```

Responsibilities:
- Declare capabilities
- Provide `executorConfiguration` (must be `Hashable` — used as Executor Store cache key)
- Keep this type intentionally light; all heavy work goes in the Executor

### 2. `LanguageModelExecutor` (heavy execution engine)

```swift
protocol LanguageModelExecutor: Sendable {
    associatedtype Configuration: Hashable, Sendable
    associatedtype Model: LanguageModel

    init(configuration: Self.Configuration) throws

    func respond(
        to request: LanguageModelExecutorGenerationRequest,
        model: Self.Model,
        streamingInto channel: LanguageModelExecutorGenerationChannel
    ) async throws

    // Optional — has a default no-op implementation
    func prewarm(model: Self.Model, transcript: Transcript)
}
```

---

## Executor Store (Automatic Lifecycle Management)

The framework uses `Configuration` (Hashable) as a cache key to reuse executors:
- Same `Configuration` → reuse existing executor
- Session deallocates → executor is automatically released (model weights / network connections freed)
- No manual memory management needed

---

## Declaring Capabilities

```swift
var capabilities: LanguageModelCapabilities {
    LanguageModelCapabilities(capabilities: [
        .toolCalling,
        .guidedGeneration,
        .reasoning
    ])
}

// App can inspect capabilities before use:
if selectedModel.capabilities.contains(.guidedGeneration) {
    let response = try await session.respond(to: "...", generating: MySchema.self)
}
```

Requesting an unsupported capability throws `LanguageModelError.unsupportedCapability(_:)`.

---

## Six Transcript Entry Types

The framework defines these six entry types (from transcript). Your executor must map all of them to your model's native roles:

| Framework entry | Typical mapping |
|---|---|
| `instructions` | System role |
| `prompt` | User role |
| `response` | Assistant role |
| `toolCalls` | Assistant role (if model has no dedicated tool role) |
| `toolOutput` | Tool role (or User role if unsupported) |
| `reasoning` | Assistant role (if model has no dedicated reasoning role) |

If your model does define a dedicated tool or reasoning role, route there instead. Your executor stays in control of the mapping.

---

## Streaming: Sending Results Back via Channel

`respond(...)` is always treated as a stream — even for one-shot models. Emit events through the channel:

Recommended event **order** (from transcript): metadata first, then usage, then text deltas. Sending metadata and usage up front means the developer doesn't have to wait through the whole stream to learn what the request costs.

```swift
func respond(
    to request: LanguageModelExecutorGenerationRequest,
    model: MyLanguageModel,
    streamingInto channel: LanguageModelExecutorGenerationChannel
) async throws {

    let entryID = ...

    // 1. Send metadata first (model ID, request ID for logging/debugging)
    await channel.send(.response(entryID: entryID, action: .updateMetadata([
        "modelID": "my-model-2026-06-08",
        "requestID": request.id.uuidString
    ])))

    // 2. Send token usage (prompt counts for accounting)
    await channel.send(.response(
        entryID: entryID,
        action: .updateUsage(
            input: .init(totalTokenCount: totalTokens, cachedTokenCount: cachedTokens),
            output: .init(totalTokenCount: 0, reasoningTokenCount: 0)
        )
    ))

    // Channel closes automatically when method returns or throws
}
```

Available event types:
- `.response(...)` — text deltas, metadata, token usage, citations
- `.reasoning(...)` — reasoning text, reasoning signatures
- `.toolCalls(...)` — argument streaming for tool calls

---

## Handling Unsupported GenerationOptions

```swift
func respond(to request: ...) async throws {
    // Model only supports temperature, not greedy sampling:
    if request.generationOptions.samplingMode == .greedy {
        // Approximate greedy with temperature = 0
    }
}
```

Approximate unsupported options rather than ignoring or throwing.

---

## `prewarm`: Reduce First-Request Latency

```swift
// Optional — override to eager-load assets before first request
func prewarm(model: Self.Model, transcript: Transcript) {
    // Load model weights into memory, or open a network connection
    // so the first respond() call has no initialization delay
}
```

> **Important (from transcript):** `prewarm` is not guaranteed to run. Your `respond()` must also handle lazy loading — e.g., load weights on first call and cache them. Either path (prewarm or lazy) should result in weights loading exactly once. For server-backed models, `prewarm` can simply be a no-op.

---

## Transcript Mapping

Executor must translate generic `Transcript` entries to the underlying model's native format. If the model lacks dedicated support for a role, map it to the nearest equivalent:
- `Reasoning` entry → treat as `Assistant` role
- `ToolCalls` entry → treat as `Assistant` role (if model has no function calling support)

No verified code for mapping — described conceptually in the transcript.

---

## State Management (KV Cache)

Executors can cache the previous transcript and compare it against the new one on each turn. If the new transcript is only appended content, process only the delta instead of re-running the full conversation history.

No verified code — described conceptually.

---

## Distribution Guidelines

| Concern | Recommendation |
|---|---|
| Distribution | Swift Package Manager (Git URL) |
| API key security | Never hardcode — use token provider + Keychain |
| Error handling | Use `LanguageModelError` for common failures (context overflow, rate limits); custom error types only for service-specific business logic |
| Performance metadata | Attach tokens/sec, time-to-first-token via typed accessors |
| Privacy transparency | Clearly declare whether model is on-device or cloud-based |
| Cloud model security | See Device Attestation session to prevent tampering |

---

## Custom Segments (Future Modalities)

Allows defining new input modalities (audio, video, specialized data) that the framework passes through to the executor. Enables future capability expansion without protocol changes. No verified code.

---

## Architecture Summary

```
App
  ↓ LanguageModelSession(model: MyModel())
Foundation Models Framework
  ↓ LanguageModel → executorConfiguration (Hashable cache key)
Executor Store (caches Executor instances)
  ↓ LanguageModelExecutor.respond(...)
Underlying model (local weights / network API)
  ↓ channel.send(...)
Foundation Models Framework → streams back to app
```

---

## Related Sessions
- [What's new in the Foundation Models framework](../What%27s%20new%20in%20the%20Foundation%20Models%20framework/)
- [Build agentic app experiences with the Foundation Models framework](../Build%20agentic%20app%20experiences%20with%20the%20Foundation%20Models%20framework/)
- *Device Attestation* — securing cloud-backed model integrations

---

## Resources
- [Foundation Models documentation](https://developer.apple.com/documentation/FoundationModels)
- [Core AI Models (GitHub)](https://github.com/apple/coreai-models)
- [MLX Swift LM (GitHub)](https://github.com/ml-explore/mlx-swift-lm)
- Session page: https://developer.apple.com/videos/play/wwdc2026/339/
