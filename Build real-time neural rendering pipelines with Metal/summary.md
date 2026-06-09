# Build real-time neural rendering pipelines with Metal

# WWDC 2026: Build Real-Time Neural Rendering Pipelines with Metal

## Overview
This session explores the integration of machine learning (ML) into real-time rendering pipelines using **Metal 4**. Apple demonstrates how developers can move beyond traditional analytical rendering methods by incorporating neural networks to improve quality, performance, and memory efficiency. The session covers three tiers of integration: high-level "black-box" APIs for quick deployment, model execution within command buffers, and low-level shader-based tensor operations for custom, hardware-accelerated networks.

---

## Key Concepts, APIs, and Frameworks

*   **MetalFX:** A platform-integrated solution providing high-performance neural denoising and upscaling. It is specifically designed for the low-latency demands of real-time viewports and games.
*   **Metal 4 ML Command Encoder:** Allows developers to load pre-trained models (e.g., exported from PyTorch as `.mtl` packages) and execute them directly within a command buffer, eliminating context-switching overhead.
*   **TensorOps API:** The most flexible layer, providing building blocks to design and execute custom models directly within shaders. It is optimized to leverage the **neural accelerator** found in M5 and A19 Pro chips.
*   **Neural Rendering Techniques:** The session highlights applications such as neural denoising, neural tone mapping, and learned sky-probe illumination.

---

## Demonstrated Techniques and Patterns

### 1. MetalFX Best Practices
To achieve production-quality results with MetalFX, the session emphasizes three critical areas:
*   **Auxiliary Input Quality:** Keep inputs (diffuse albedo, depth, etc.) as clean as possible. Use debug views and GPU captures to validate that inputs match what the model expects.
*   **Material Handling:** For reflections and transmission (glass), use "primary surface replacement"—blending geometric properties by the Fresnel term to ensure reflections remain sharp.
*   **Motion Vectors:** Always provide de-jittered motion vectors to prevent edge shimmering. The session provides logic to compute these by subtracting jittered deltas from current and previous camera matrices.

### 2. Neural Tone Mapping
Developers can replace complex, multi-stage post-processing pipelines (color grading, film emulation) with a single neural evaluation (e.g., based on the **HDRNet** architecture). This involves:
*   Training a model offline.
*   Exporting it as an `.mtl` package.
*   Dispatched as part of the main render loop to handle color transformation in one pass.

### 3. Online Training with TensorOps
For scenarios where static pre-computation fails (e.g., dynamic day/night lighting), developers can implement **online training** directly in the shader:
*   **MLP Structure:** Implement a small Fully Connected Multilayer Perceptron (e.g., 3-4-4-3 architecture).
*   **Cooperative Tensors:** Use `CMDigGroup` execution scope in compute shaders. This allows threads to share storage for matrix multiplications, avoiding expensive round-trips to main memory.
*   **Backpropagation:** Implement the error-correction loop directly in the shader to allow the model to adapt to scene changes frame-by-frame.

---

## Practical Takeaways for Developers

*   **Start with MetalFX:** If you are building a real-time viewport or game, adopt MetalFX for immediate gains in denoising and upscaling. Use the `transparency overlay` and `denoiser strength mask` to protect specific effects like particles or skies.
*   **Consolidate Post-Processing:** If your color pipeline has become bloated, train an offline model to encapsulate those transformations and deploy it via the ML command encoder to save GPU cycles.
*   **Leverage Neural Accelerators:** For highly specific, small-scale tasks (like light probing), use the TensorOps API to write tiny, performant networks that run inline with your existing shader code.
*   **Optimize Memory:** Use cooperative tensors and group-shared memory to keep data on the GPU and minimize latency.
*   **Further Learning:** Consult the *Metal Performance Primitives programming guide* and the *Go Further with Metal 4 Games* session for detailed code snippets and reactive mask implementations.

<!-- resources -->

---

## Resources

- [Training a neural network to render irradiance in real time](https://developer.apple.com/documentation/Metal/training-a-neural-network-to-render-irradiance-in-real-time)
- [Metal sample code library](https://developer.apple.com/documentation/Metal/metal-sample-code-library)
- [Download the Metal Performance Primitives (MPP) Programming Guide](https://developer.apple.com/download/files/Metal-Performance-Primitives-Programming-Guide.pdf)
- [Understanding the Metal 4 core API](https://developer.apple.com/documentation/Metal/understanding-the-metal-4-core-api)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/359/5/9da4a720-0dcb-4b8e-b61b-ba8310a61f29/downloads/wwdc2026-359_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/359/5/9da4a720-0dcb-4b8e-b61b-ba8310a61f29/downloads/wwdc2026-359_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/359/
