# Meet Core AI

# WWDC 2026: Meet Core AI

## Session Overview
This session introduces **Core AI**, Apple’s new comprehensive framework for on-device AI execution. Designed to replace existing fragmented workflows, Core AI provides an end-to-end ecosystem for the entire model lifecycle—from optimization and conversion to debugging and production integration. The framework is built to leverage the full power of Apple Silicon (CPU, GPU, and Neural Engine) while maintaining strict memory safety through a modern Swift API. The session highlights how developers can scale from simple, small-scale models to complex, 70B parameter LLMs, all running locally on-device with zero server costs.

## Key Concepts, APIs, and Frameworks
*   **Core AI Framework:** The primary Swift API for loading and running models. It uses modern features like non-escapable types to ensure memory safety without sacrificing performance.
*   **Core AI Torch:** A Python package that enables direct conversion of PyTorch models into the optimized Core AI format.
*   **NDArray:** The foundational data structure in Core AI, used to pass multi-dimensional input/output data to inference functions.
*   **States:** A powerful feature that allows for in-place updates during inference. This is crucial for transformer-based models to implement key-value caching, avoiding costly recomputations.
*   **Model Specialization:** The process of compiling a model for a specific device. Since this is computationally expensive, the framework allows developers to programmatically manage this cache and "pre-warm" models.
*   **Developer Toolchain:**
    *   **Instruments:** New dedicated profiling tools for monitoring inference latency.
    *   **Visual Debugger:** A tool to inspect intermediate tensor values and trace operations back to the original Python source code.
    *   **Debug Gauge:** A streaming monitor in Xcode to spot performance issues in real-time.

## Demonstrated Techniques
*   **Model Conversion Flow:** The session demonstrates converting a PyTorch transformer model by using `torch.export` with dynamic shapes, followed by Core AI’s decomposition and conversion pipeline.
*   **Numerics Verification:** Developers are encouraged to use Python bindings to compare the outputs of the original PyTorch model against the converted Core AI model to ensure precision.
*   **Performance Optimization (KV Caching):** To solve latency growth in sequence-based models, the speaker demonstrated adding `register_buffer` in PyTorch to define caches, which become "States" in Core AI. This allows the model to only process new inputs rather than recalculating the entire history.
*   **Memory Management:** Use of `NDArray.MutableView` to provide safe, direct access to the model's backing storage.
*   **Ahead-of-Time (AOT) Compilation:** To mitigate the "first-run" specialization penalty, developers can compile models on their development machine to reduce the work required on the user's device.

## Practical Takeaways for Developers
1.  **Iterative Workflow:** Start by authoring in PyTorch, convert to Core AI using the `coreai-torch` package, and verify numeric consistency before integrating into Swift.
2.  **Avoid In-App Specialization:** Do not trigger model specialization (which is resource-intensive) during time-sensitive user interactions. Check the model cache availability first and handle loading states gracefully.
3.  **Optimize Loops:** For performance-critical apps, utilize lower-level APIs to pre-allocate memory for NDArrays, avoiding costly allocations or layout conversions during inference.
4.  **Leverage the Ecosystem:** Don't build from scratch; the **Core AI Models repository** contains popular models already optimized for Apple Silicon and higher-level Swift abstractions for specific model families.
5.  **Use Modern Debugging:** If an app feels sluggish, use the new Core AI Instruments immediately. Understanding whether latency stems from compilation/specialization or inference compute is the first step toward a performant AI-powered app.

<!-- resources -->

---

## Resources

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [Managing model specialization and caching](https://developer.apple.com/documentation/CoreAI/managing-model-specialization-and-caching)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/324/4/3b67b624-4060-495f-9ba7-659805ee6b88/downloads/wwdc2026-324_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/324/4/3b67b624-4060-495f-9ba7-659805ee6b88/downloads/wwdc2026-324_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/324/
