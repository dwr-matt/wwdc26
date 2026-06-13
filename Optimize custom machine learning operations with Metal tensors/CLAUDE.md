# Optimize custom machine learning operations with Metal tensors — Session Notes

Sources:
- Transcript: full walkthrough (Xiao, GPU software engineer), building on a prior "M5 machine learning" talk's basic matmul kernel
- Documentation fetches:
  - https://developer.apple.com/tutorials/data/documentation/metal/running-inline-ml-operations-in-a-shader-with-metal-4.json — verified, has MSL code for tensor declaration, slicing, matmul2d, cooperative tensors, element-wise ops, store. Does NOT cover quantized tensors, scales planes, reduceRows, mapIterator, or Flash Attention.
  - https://developer.apple.com/tutorials/data/documentation/metal/machine-learning-passes.json — verified, covers Metal 4 ML passes (MTL4MachineLearningCommandEncoder etc.), no MSL code
  - https://developer.apple.com/tutorials/data/documentation/metalperformanceshaders.json — verified, MPS overview only
- Quantized tensor setup, scales planes, slicing quantized tensors, and the full Flash Attention implementation (execution_cmd_group, reduceRows, mapIterator, isCompatibleAsLeft/RightInput) are **transcript-described only** — no MSL code retrievable from fetched docs. Treat as conceptual/structural.
- CoreAI/SAM3 integration steps are transcript-described only.

---

## 1. Where This Sits in the Stack

```
CoreAI / MLX           — high-level, deploy models with minimal code
Metal Performance Shaders — mid-level, pre-tuned kernels
Metal Performance Primitives + Tensor Ops — low-level, write GPU kernels directly
```

Reasons to drop to this level: implement custom ops that plug into CoreAI; contribute to frameworks like MLX/llama.cpp; build Metal-based apps directly.

**Tensor Ops** = MSL API accelerating tensor ops (matmul, convolution) across all Apple Silicon GPU generations automatically, including the **Neuron Accelerator** — new hardware block in M5 family, located in each shader core, accelerates dense compute-bound work (e.g. LLM prefill).

---

## 2. Quantized Data (transcript-described)

Motivation: inference is memory-bandwidth bound; quantization (e.g. 16-bit → 4-bit weights + scale factors) reduces traffic and memory footprint.

Tensor Ops native quantized type support:
- macOS/iOS 26: 4-bit and 8-bit integer types
- macOS/iOS 27: adds 4-bit and 8-bit floating point types, 2-bit integer types

**Creating a quantized tensor**: same as a regular tensor — fill descriptor properties, specify a quantized data type, call `newTensorWithDescriptor` on the device.

**Scales Plane** (macOS/iOS 27): scale factors become an "auxiliary plane" attached to the main tensor descriptor, supporting the FP8 E8M0 block-wide scale factor format — each scales-plane element applies to a block of elements in the data plane. Setup: create a descriptor for the scales plane (data type + block factor) → create an auxiliary plane map marking it as scales → attach to the original tensor descriptor. Quantized data, scales, and metadata pack into a single tensor object.

---

## 3. Matrix Multiplication with Tensor Ops (verified code from docs)

Basic kernel signature — tensors declared with rank-2 extents, `int` index types required:

```metal
kernel void matrix_multiplication_kernel(
    uint2 threadgroupIdentifier [[threadgroup_position_in_grid]],
    tensor<device half, dextents<int, 2>> sourceA,
    tensor<device half, dextents<int, 2>> sourceB,
    tensor<device half, dextents<int, 2>> destination)
```

Slicing into tiles by threadgroup ID (static extents skip edge handling; `dynamic_extent` lets the compiler infer shared dims at runtime):

```metal
const int TileSize = 64;
int tileOriginX = TileSize * threadgroupIdentifier.x;
int tileOriginY = TileSize * threadgroupIdentifier.y;

auto sliceA = sourceA.slice<dynamic_extent, TileSize>(0, tileOriginY);
auto sliceB = sourceB.slice<TileSize, dynamic_extent>(tileOriginX, 0);
auto destinationSlice = destination.slice<TileSize, TileSize>(
    tileOriginX, tileOriginY);
```

Configuring matmul2d (output tile 64x64, inner dim inferred at runtime, 4 SIMD groups):

```metal
constexpr auto descriptor = matmul2d_descriptor(
    TileSize, TileSize, dynamic_length_v<int>);
matmul2d<descriptor, execution_simdgroups<4>> operation;
```

**Quantized version** (transcript-described, no MSL shown): declare a scales-plane type with FP8 E8M0 and block size 32x1 (32 data-plane elements share one scale), declare a full tensor type combining FP8 data + scales plane. Bind to buffer binding points, or use `tensor inline` (instead of `tensor` handle) to build a temporary tensor directly on the shader stack from buffer pointers/metadata. When slicing, the data plane and scales plane are sliced together according to block size. Passing a quantized tensor straight into matmul2d makes Tensor Ops handle dequantization automatically.

**Fallback for custom/unsupported quant formats** (transcript-described):
1. Each thread loads quantized data from device memory, dequantizes to F16 in threadgroup memory, passes as inline threadgroup tensor — costs extra load/store through threadgroup memory.
2. Better: dequantize directly into a **cooperative tensor** (thread-private registers), skip the threadgroup-memory round trip.

Note: newer 2-bit/4-bit types have additional alignment requirements — check Metal docs.

---

## 4. Cooperative Tensors (verified code from docs)

Distribute intermediate result storage across thread-private registers of the threads participating in the operation — avoids read/write latency to device/threadgroup memory.

```metal
auto cooperativeTensor = operation.get_destination_cooperative_tensor<
    decltype(sliceA),
    decltype(sliceB),
    half>();

operation.run(sliceA, sliceB, cooperativeTensor);
```

Element-wise postfix operation (e.g. activation):

```metal
auto threadElements = cooperativeTensor.get_capacity();
for (int element = 0; element < threadElements; element++) {
    auto value = cooperativeTensor[element];
    cooperativeTensor[element] = relu(value);
}

half relu(half value) {
    return max(value, (half)0.0f);
}
```

Store back to destination:

```metal
cooperativeTensor.store(destinationSlice);
```

Requirements: GPU family apple10+, macOS 26.0+/Xcode 26.0+.

---

## 5. Flash Attention with Tensor Ops (transcript-described, not doc-verified)

Standard attention: Q×K → row-wise softmax → ×V. Flash Attention fuses these into one kernel, avoiding writing the intermediate matrix to memory.

Key techniques described:

1. **`execution_cmd_group`** — custom cmd-group mapping so each cmd group owns complete rows of the intermediate matrix; enables row-wise softmax without cross-cmd-group data exchange. Each cmd group does an independent matmul in parallel, slicing inputs by its own cmd-group ID.
2. Store the Q×K intermediate result in a **cooperative tensor** — feeds directly into the next step without a memory round trip.
3. **`reduceRows`** — row-wise reduction over a cooperative tensor (e.g. row max for softmax numerical stability). Threads exchange data to compute per-row max; result goes into a second cooperative tensor (different shape from the source).
4. **`mapIterator`** — bridges between cooperative tensors of different shapes: iterate the 2D source cooperative tensor, map each element's iterator to its corresponding row-max iterator in the reduction output, dereference both to compute softmax and write back into the source cooperative tensor.
5. Multiply the softmax result by V: in macOS 26 this required storing to threadgroup memory first, but cooperative tensors can now be used **directly as matmul inputs** — call `getLeftInputCooperativeTensor` (passing the source cooperative tensor) to get a usable input for the second matmul operation.
6. **Compatibility check** — not every cooperative tensor can be reused as an input; layout may differ by data type/other factors. Call `isCompatibleAsLeft` / `isCompatibleAsRight` (Input) — if `true`, use directly; if `false`, store/reload through threadgroup memory to convert layout. Either way, `op.run` is called the same way.

---

## 6. CoreAI Integration: SAM3 Example (transcript-described)

1. Define the custom attention kernel body as a string (MSL) in Python; register it as a Torch metal kernel object (see *Dive into CoreAI Model Authoring and Optimization* for the registration mechanism)
2. Replace the default HuggingFace attention implementation with one that calls the custom kernel
3. Load the model from HuggingFace, export from PyTorch as an optimized CoreAI asset
4. Run inference: SAM3 does promptable concept segmentation — image + text prompt (e.g. "car") → segmentation mask
5. Result: car correctly highlighted — confirms the custom attention kernel is fully integrated into the model

---

## 7. Background: Metal 4 Machine Learning Passes (verified, no MSL code)

Metal 4 lets Core ML models run inline within render/compute/blit pass workflows, without CPU sync.

- `MTL4MachineLearningCommandEncoder` — encodes inference commands within an ML pass
- `MTL4MachineLearningPipelineDescriptor` / `MTL4MachineLearningPipelineState` / `MTL4MachineLearningPipelineReflection` — pipeline config/state/metadata
- MSL tensor types: `tensor_handle` (CPU-created MTLTensor handle), `tensor_inline` (GPU-side view into tensor/buffer), `cooperative_tensor`, `tensor_offset` tag (GPU-side slicing without new descriptors, e.g. `tensor<device float, dextents<int, 2>, tensor_handle, tensor_offset>`)
- Setup pipeline: CoreML model → `metal-package-builder` tool → Metal ML package → added to Xcode project → compiled into Metal library
- System picks GPU or ANE automatically; when ANE runs a model, GPU can do independent render/compute work concurrently
- Synchronization: `MTLStages.machineLearning` stage; consumer/producer queue-barriers coordinate with render/compute passes
- Limitation: ML encoders only run *existing* Core ML models — cannot build/modify networks (use Core ML or MPS Graph for that)
- Debuggable via Metal debugger in Xcode 26

---

## Related Sessions
- *Dive into Core AI model authoring and optimization* — custom Metal kernel registration mechanism, SAM3 conversion pipeline
- "M5 machine learning talk" (referenced, not in this repo by that name) — basics of writing a high-performance matmul kernel with Tensor Ops

---

## Resources
- [Running inline ML operations in a shader with Metal 4](https://developer.apple.com/documentation/Metal/running-inline-ml-operations-in-a-shader-with-metal-4)
- [Machine learning passes](https://developer.apple.com/documentation/Metal/machine-learning-passes)
- [Metal Performance Primitives (MPP) Programming Guide PDF](https://developer.apple.com/download/files/Metal-Performance-Primitives-Programming-Guide.pdf) — not fetched (PDF)
- [Metal Performance Shaders](https://developer.apple.com/documentation/MetalPerformanceShaders)
- Session page: https://developer.apple.com/videos/play/wwdc2026/330/
