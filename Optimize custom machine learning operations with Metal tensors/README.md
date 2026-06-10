# Optimize custom machine learning operations with Metal tensors

# Session Summary: Optimize Custom Machine Learning Operations with Metal Tensors

## Overview
This session explores how developers can leverage the **Tensor Ops** library—a component of the Metal Shading Language (MSL) API—to create high-performance, custom machine learning kernels on Apple Silicon. The presenter demonstrates how to move beyond high-level frameworks like CoreAI and MLX to write low-level GPU kernels, specifically focusing on handling quantized data and implementing complex operations like Flash Attention. The session culminates in a demonstration of integrating a custom kernel into a production model (SAM3) via CoreAI.

## Key Concepts, APIs, and Frameworks
*   **Tensor Ops Library:** A low-level API for accelerating tensor operations (matrix multiplication, convolution) across all Apple Silicon generations.
*   **Neuron Accelerator:** A dedicated hardware block within the M5 chip family, integrated into each shader core, designed to accelerate dense compute-bound tasks like LLM pre-fill stages.
*   **Quantization:** A technique to reduce memory bandwidth bottlenecks by using lower-precision data types (4-bit/8-bit integers and floating-point types). 
*   **Cooperative Tensors:** A memory management feature that distributes storage across thread-private registers, avoiding the overhead of writing intermediate results to thread-group memory.
*   **CoreAI:** Used for high-level model deployment and integrating custom-written Metal kernels into PyTorch-based workflows.

## Code Patterns and Techniques
### 1. Handling Quantized Data
*   **Descriptor-Based Setup:** Quantized tensors are created via `MTLDevice` similarly to standard tensors, but by specifying a quantized data type in the descriptor.
*   **Scales Planes:** In macOS/iOS 27, scale factors are treated as an "auxiliary plane" attached to the main tensor descriptor. This supports formats like FP8 EAM0.
*   **Inline Tensors:** Developers can create temporary tensors directly on the shader stack using `tensor inline` rather than `MTLBuffer` bindings to reduce host-side overhead.

### 2. Matrix Multiplication and Slicing
*   **Parallelism:** Work is divided across thread groups. The `slice` operation is used on input/output tensors based on the `threadgroup_id` to maximize cache hits.
*   **Dequantization:** Tensor Ops handles dequantization automatically when passing quantized tensors to matrix multiplication operators. For custom formats, developers can load data into registers and use **Cooperative Tensors** to maintain high throughput.

### 3. Flash Attention Implementation
*   **Execution Scopes:** Use `execution_cmd_group` to ensure matrix operations remain local to a thread group, allowing for efficient in-place softmax computation.
*   **Row Reductions:** The `reduceRows` function calculates row-wise maximums, returning a cooperative tensor.
*   **Iterator Mapping:** `mapIterator` is used to bridge shapes between the intermediate cooperative tensor and the reduction output, enabling element-wise softmax calculation.
*   **Kernel Compatibility:** Use `isCompatibleAsLeft` or `isCompatibleAsRight` to verify if a cooperative tensor can be reused as an input for subsequent operations without needing a "round trip" through thread-group memory.

## Practical Takeaways for Developers
*   **Prioritize Tensor Ops:** Always aim to feed quantized data directly into Tensor Ops to automatically leverage the latest Apple Silicon hardware (like the M5 Neuron Accelerator).
*   **Minimize Memory Traffic:** Use **Cooperative Tensors** to keep intermediate results in registers rather than thread-group memory, which significantly boosts performance for algorithms like Flash Attention.
*   **Alignment Matters:** Be aware that newer 2-bit/4-bit data types have specific alignment requirements; consult the Metal documentation to avoid runtime errors.
*   **Seamless Integration:** You do not have to abandon high-level tools to use custom kernels. You can define custom MSL kernels in Python, register them as `Torch` metal kernel objects, and inject them into standard model architectures for optimized deployment via CoreAI.

<!-- resources -->

---

## Resources

- [Running inline ML operations in a shader with Metal 4](https://developer.apple.com/documentation/Metal/running-inline-ml-operations-in-a-shader-with-metal-4)
- [Machine learning passes](https://developer.apple.com/documentation/Metal/machine-learning-passes)
- [Download the Metal Performance Primitives (MPP) Programming Guide](https://developer.apple.com/download/files/Metal-Performance-Primitives-Programming-Guide.pdf)
- [Metal Performance Shaders](https://developer.apple.com/documentation/MetalPerformanceShaders)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/330/4/0ff2c290-e47b-4d88-8a8f-0634e11506a4/downloads/wwdc2026-330_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/330/4/0ff2c290-e47b-4d88-8a8f-0634e11506a4/downloads/wwdc2026-330_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/330/
