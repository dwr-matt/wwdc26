# What's new in the Foundation Models framework

# Session Summary: What's new in the Foundation Models framework

## Overview
This session introduces significant expansions to Apple's **Foundation Models Framework**, focusing on modularity, agentic capabilities, and increased model accessibility. The 2027 release centers on three pillars: OS-level integration, support for a wider variety of models (on-device, server-side, and third-party), and new primitives that allow developers to build sophisticated, autonomous agentic experiences. A major highlight is the move toward open-sourcing the core framework, enabling consistent Swift-based AI development across Apple platforms and beyond (e.g., Linux).

---

## Key Concepts, APIs, and Frameworks

### 1. Model Updates & Modalities
*   **On-Device Model:** Rebuilt from the ground up, the new on-device model offers improved logic, faster tool calling, and now supports **Vision capabilities**. Developers can pass `UIImage`, `NSImage`, `CGImage`, or file URLs directly into prompt builders without needing to pre-process or crop them.
*   **Private Cloud Compute (PCC):** A new server-side model that offers increased scale and a 32,000-token context window. It features **"Reasoning" capabilities**, where the model spends time "thinking" before responding, controlled by a `reasoningLevel` parameter. It is private by design and requires no API key or complex authentication.
*   **Model Abstraction Layer:** A new protocol-oriented architecture allows developers to swap between local and server models seamlessly. Third-party providers like Anthropic and Google are releasing Swift packages to integrate their models directly into the framework.

### 2. Agentic Primitives
*   **Dynamic Profiles:** A new primitive for building agents that can autonomously switch "modes." A profile acts as a data structure containing instructions, tools, and specific model configurations. The framework manages the transition between these contexts, allowing an app to pivot from an "Analysis" mode to a "Brainstorming" mode dynamically.

### 3. New Tooling
*   **System Tools:** Native tools now available to all sessions, including a **Barcode Reader**, **OCR** for structured text extraction, and a **Spotlight Search** tool for local Retrieval-Augmented Generation (RAG).
*   **Evaluations Framework:** A new framework designed to measure the quality, accuracy, and statistical impact of prompt changes, helping developers deliver more reliable, non-deterministic AI features.
*   **FM CLI:** A new command-line interface for macOS that allows developers to access the on-device and PCC models directly from the terminal for scripting and productivity tasks.

---

## Code Patterns & Techniques

*   **Handling Images:** Vision-enabled prompts are handled by inserting image attachments directly into the prompt builder:
    ```swift
    // The framework accepts various image types natively
    let prompt = PromptBuilder()
        .add(image: myUIImage)
        .add(text: "Describe this craft.")
    ```
*   **Dynamic Profile Implementation:** Use a protocol-conforming structure to define context and use modifiers for model configuration:
    ```swift
    // Defining a profile with a specific model and reasoning capability
    let brainstormProfile = Profile(instructions: "...", tools: [...])
        .modifier(ModelModifier(PCC))
        .modifier(ReasoningLevelModifier(.deep))
    ```
*   **Usage Tracking:** To manage billing (especially for third-party models), use the `usage` property on sessions to monitor token consumption, including cached input tokens and reasoning tokens.

---

## Practical Takeaways for Developers

1.  **Open Source Strategy:** The framework is becoming open source, including the `Foundation Models Framework Utilities` package, which will be updated out-of-band from OS releases to provide access to experimental building blocks.
2.  **Privacy & Costs:** PCC is free for developers with fewer than 2 million first-time downloads, with additional limits for iCloud+ subscribers. If using third-party models, always use secure storage (Keychain) for credentials—never embed keys in binaries.
3.  **Modernizing Apps:** 
    *   Use the new token-counting APIs in iOS 26.4+ to adapt UI elements to the model's context window.
    *   Transition to the `LanguageModel` protocol to make your app model-agnostic, allowing users to choose between local, PCC, or third-party models.
4.  **Adopt RAG:** Leverage the new Spotlight integration to implement local RAG without building custom vector databases, giving your models access to the user's local files.
5.  **WatchOS:** The Foundation Models Framework is now available on `watchOS 27`, enabling powerful, PCC-backed AI directly on the wrist.

<!-- resources -->

---

## Resources

- [Expanding generation with tool calling](https://developer.apple.com/documentation/FoundationModels/expanding-generation-with-tool-calling)
- [Analyzing images with multimodal prompting](https://developer.apple.com/documentation/FoundationModels/analyzing-images-with-multimodal-prompting)
- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/241/6/900558cb-1997-490a-9aac-2461b209e578/downloads/wwdc2026-241_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/241/6/900558cb-1997-490a-9aac-2461b209e578/downloads/wwdc2026-241_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/241/
