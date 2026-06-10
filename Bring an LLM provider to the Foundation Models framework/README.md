# Bring an LLM provider to the Foundation Models framework

## WWDC 2026: Bring an LLM Provider to the Foundation Models Framework

### Session Overview
This session introduces the expansion of Apple’s **Foundation Models framework**, transforming it from a tool for native Apple models into an open, extensible ecosystem. Apple has established a public protocol that allows developers and model providers to integrate virtually any LLM—whether running locally, via Private Cloud Compute (PCC), or through third-party APIs like Anthropic’s Claude or Google’s Gemini—into their apps using a unified, consistent API. By adopting this protocol, model providers can distribute their models as Swift packages, enabling developers to swap out underlying AI models with minimal code changes.

### Key Concepts & Frameworks
*   **Foundation Models Protocol:** The core abstraction layer. Every model, regardless of origin, must conform to this protocol to ensure compatibility across the Apple ecosystem (iOS, macOS, visionOS, watchOS, and Linux).
*   **Language Model & Executor:** The two pillars of the integration. The `LanguageModel` defines configuration and capabilities, while the `LanguageModelExecutor` acts as the engine that performs inference, handles state, and manages resources.
*   **The Executor Store:** The framework’s built-in caching mechanism. It uses the model's `Configuration` (which must be `Hashable`) as a lookup key to manage and reuse executors, optimizing resource lifecycle management and minimizing overhead.
*   **Transcript:** A structured representation of the conversation history. It includes entries for instructions, user prompts, tool calls, and model responses. The executor is responsible for mapping these entries to the inference engine's native format.
*   **Custom Segments:** An extensibility feature that allows developers to define new modalities (e.g., audio, video, or specialized data) that the framework can pass through the protocol, future-proofing integrations against new model capabilities.

### Code Patterns & Techniques
*   **Separation of Concerns:** The framework relies on a clean split where the `LanguageModel` provides configuration and the `Executor` performs the work. This keeps implementation logic "trivial" and handles teardown (releasing weights/connections) automatically when a session deallocates.
*   **The `pre-warn` Method:** A vital optimization hook for executors. It allows developers to perform expensive setup tasks—like loading model weights or opening network connections—eagerly, before the first request is made, to reduce latency.
*   **Mapping Transcript Entries:** Executors must act as a translation bridge. A key pattern demonstrated is the mapping of generic `Transcript` roles to specific model-side roles (e.g., mapping "Reasoning" or "Tool Calls" to an "Assistant" role if the specific model lacks dedicated support).
*   **Streaming & Events:** The `respond` function is always treated as a stream by the framework. Even if the underlying model is one-shot, the executor must emit events (text deltas, usage stats, metadata) to the channel so the UI can update in real-time.
*   **State Management:** Executors can maintain state (e.g., KV caches) by comparing the new `Transcript` against the one cached from the previous turn, allowing the model to process only appended content rather than re-running the full conversation history.

### Practical Takeaways for Developers
*   **Distribution via Swift Packages:** Model providers should use the Swift Package Manager for distribution. Since the system is decentralized, the Git repository URL serves as the primary distribution channel for developers.
*   **Security & Auth:** Do not hardcode API keys. Implement token providers or sign-in flows and store credentials securely in the Keychain. For cloud-backed models, consult the session on **Device Attestation** to prevent tampering and unauthorized access.
*   **Error Handling:** Use the built-in `LanguageModelError` for common issues like context overflow or rate limits. Only define custom error types when the failure is specific to your service’s business logic (e.g., subscription status).
*   **Performance Metadata:** Differentiate your package by attaching useful metadata (tokens per second, time-to-first-token) to responses. Use typed accessors to make this data easily discoverable for the end developer.
*   **Privacy Transparency:** Always communicate the privacy characteristics of your model. Whether a model is on-device or cloud-based carries different security profiles, and it is the responsibility of the package author to be transparent with the end user.

<!-- resources -->

---

## Resources

- [Foundation Models](https://developer.apple.com/documentation/FoundationModels)
- [Core AI Models](https://github.com/apple/coreai-models)
- [MLX Swift LM on GitHub](https://github.com/ml-explore/mlx-swift-lm)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/339/4/334f1ee9-4263-4c86-9b10-632f0f2edab1/downloads/wwdc2026-339_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/339/4/334f1ee9-4263-4c86-9b10-632f0f2edab1/downloads/wwdc2026-339_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/339/
