# Dive into Core AI model authoring and optimization

# WWDC 2026: Dive into Core AI Model Authoring and Optimization

## Overview
This session introduces the **CoreAI** ecosystem, a suite of tools and libraries designed to streamline the deployment, optimization, and debugging of machine learning models on Apple Silicon. The presentation focuses on a Python-centric workflow that bridges PyTorch development with on-device Apple performance. It covers the end-to-end lifecycle—from initial model conversion and compression using CoreAI Opt, to deep-dive troubleshooting with the new CoreAI Debugger, and advanced techniques like model re-authoring and custom Metal kernel integration for specialized performance.

---

## Key Concepts, APIs, and Frameworks

### CoreAI Ecosystem
*   **CoreAI Models Repository:** An open-source repository of pre-optimized models (including generative architectures) and Swift packages for LLM deployment.
*   **CoreAI Skills:** An integration for coding assistants that acts as a domain-knowledge expert, helping developers translate high-level requirements into clear deployment plans, including PyTorch code adjustments and optimization strategies.
*   **CoreAI Torch:** The primary Python library for converting PyTorch exported programs into CoreAI-ready assets.
*   **CoreAI Opt:** A library for config-driven model compression, supporting techniques like int4, int8, fp4, and fp8 quantization, as well as quantization-aware training (QAT).
*   **CoreAI Debugger:** A new standalone visual tool for inspecting model structure, validating inference, comparing intermediate tensor values between PyTorch and CoreAI, and diagnosing quantization issues.

---

## Code Patterns & Techniques

### The Core Conversion Pipeline
1.  **Export:** Use `torch.export` to capture the computational graph (weights, operations, shapes).
2.  **Optimize:** Pass the exported model to `CoreAI Opt` with a compression configuration (e.g., `Presets.w4` for 4-bit per-channel quantization).
3.  **Convert:** Use the CoreAI Torch converter to bundle the model into a specialized, optimized asset.
4.  **Inference:** Execute via dictionary mapping (input names to NumPy tensors) directly in Python.

### Advanced Customization
*   **Custom Metal Kernels:** Developers can write custom kernels in Metal Shading Language (MSL) and embed them directly into the CoreAI model asset. By registering these kernels with a PyTorch reference, CoreAI can bind them together, allowing the model to carry its own optimized GPU primitives.
*   **Model Re-authoring:** For complex tasks, instead of converting an end-to-end model, developers can restructure the PyTorch source to expose individual modular functions (e.g., splitting a model into `ImageEncoder`, `TextEncoder`, and `Detector`).

---

## Practical Takeaways for Developers

*   **Debugger-Led Optimization:** Use the `Save Intermediates` API to compare PyTorch baselines against optimized models. The CoreAI Debugger helps identify "sync points" where results diverge, allowing for targeted quantization (e.g., disabling compression on sensitive layers like a detector that accounts for little of the total model size).
*   **Performance via Modularity:** Re-authoring models into distinct, callable modules provides significant runtime gains. By splitting a model, you can cache intermediate results—such as the output of an image encoder—and re-run only the necessary components when prompts change, leading to massive speedups (e.g., 76% faster inference).
*   **Leverage Existing Knowledge:** The workflow is designed to feel familiar to those who have used `coremltools`. It keeps the developer in their existing Python/PyTorch environment throughout the entire lifecycle.
*   **Use AI Assistants:** Integrate **CoreAI Skills** into your coding environment to have an agent suggest best practices, hardware-specific configurations, and boilerplate code, effectively acting as an extension of the CoreAI engineering team.
*   **Targeting Platforms:** Always consider the target hardware (iOS vs. macOS) when choosing quantization granularity and model structure. Use `CoreAI Opt` presets for common scenarios, but fall back to lower-level APIs for fine-grained control when power or memory efficiency on mobile is the priority.

<!-- resources -->

---

## Resources

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Inspecting, debugging, and profiling Core AI models](https://developer.apple.com/documentation/CoreAI/inspecting-debugging-and-profiling-core-ai-models)
- [Inspecting Core AI models with Core AI Debugger](https://developer.apple.com/documentation/CoreAI/inspecting-core-ai-models-with-core-ai-debugger)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/325/5/8d08c9d4-3c64-49e1-8590-8b76bd9ad4cb/downloads/wwdc2026-325_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/325/5/8d08c9d4-3c64-49e1-8590-8b76bd9ad4cb/downloads/wwdc2026-325_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/325/
