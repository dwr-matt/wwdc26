# Integrate on-device AI models into your app using Core AI

# WWDC 2026: Integrate on-device AI models into your app using Core AI

## Overview
This session introduces **Core AI**, Apple’s new framework for building privacy-centric, on-device intelligence. The presenter, Karina from the Core AI team, demonstrates how to integrate local large language models (LLMs) and vision transformers into a cross-platform (iOS/macOS) language learning application. By running models locally, developers can eliminate server management, reduce latency, and remove per-token costs, all while ensuring user data remains strictly on the device.

---

## Key Concepts, APIs, and Frameworks

*   **Core AI Framework:** The unified framework for loading, executing, and managing on-device AI models.
*   **Model Composition:** The session advocates for decomposing complex features into task-specific models (e.g., using a Vision model for image segmentation and an LLM for reasoning) to maintain a smaller memory footprint and higher quality outputs.
*   **Core AI Models Repository:** A central catalog providing pre-optimized models and conversion scripts (Python-based) to transform standard PyTorch models into the optimized Core AI format.
*   **Foundation Models API:** A standardized Swift API used for interacting with both Apple’s first-party models and developer-customized models. It includes support for streaming, structured output, and model sessions.
*   **Guided Generation:** A feature that allows developers to define the expected structure of AI output using macros, ensuring the model returns valid data (e.g., a vocabulary card with fixed fields) rather than freeform text.

---

## Code Patterns and Techniques

### Model Integration
Developers can use the Core AI Swift package to abstract away tensor math. 
*   **Loading:** Loading models is simplified to a single line: `let model = CoreAILanguageModel(bundle: ...)`
*   **Session Management:** The `LanguageModelSession` allows for consistent interactions, whether using Apple's built-in models or your own local variants.

### Performance & Optimization
*   **Model Specialization:** When a model is first loaded, it undergoes a "specialization" process to prepare it for the specific hardware. This is a one-time, potentially time-consuming task that should be managed via a first-run onboarding flow rather than the main user interaction loop.
*   **Ahead-of-Time (AOT) Compilation:** Using the `Core AI build` command, developers can perform heavy compilation on a development machine, creating platform-specific artifacts. This significantly reduces the time required for device-side specialization.
*   **Background Assets:** To keep the initial app binary size small, the session recommends using **Background Assets** to download models only when the user opts into the AI features.

---

## Practical Takeaways for Developers

1.  **Architecture Matters:** Avoid the "one giant model" approach. Smaller, task-specific models (e.g., under 1 billion parameters) are often more effective for on-device deployment due to memory and thermal constraints.
2.  **Manage User Expectations:** Never trigger "Model Specialization" during a time-sensitive user interaction. Use a dedicated setup screen or "feature introduction" flow to download and prepare the model in the background.
3.  **Leverage Hardware Scaling:** Design your application to be modular. You can use lighter, more efficient models on iPhones for real-time tasks, while utilizing larger, more powerful model variants on macOS for batch processing and advanced reasoning (like curriculum generation).
4.  **Use the Toolchain:** Don't write raw tensor preprocessing code unless absolutely necessary. Rely on the `Core AI Models` repository’s Swift runtime libraries for common pre- and post-processing tasks, such as mask extraction or text tokenization.
5.  **Debug with Instruments:** Use the new "Core AI" instruments in Xcode to trace model loading, specialization, and inference times to identify bottlenecks in your AI pipeline.

<!-- resources -->

---

## Resources

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/326/5/7ff038e2-12cb-4b92-9f49-1d051db7ce5d/downloads/wwdc2026-326_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/326/5/7ff038e2-12cb-4b92-9f49-1d051db7ce5d/downloads/wwdc2026-326_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/326/
