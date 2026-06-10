# Meet Core AI — Session Notes

> ⚠️ **Code note:** The transcript for this session is verbal narration only — no actual code was shown on screen in the transcript data. The README.md contains no verified code examples either. Documentation URLs are listed in Resources but WebFetch could not retrieve their content. All API names below are taken from verbal descriptions in the transcript; treat them as conceptual references, not verified syntax.

---

## What Is Core AI?

Apple's new end-to-end on-device AI inference framework. It replaces fragmented workflows with a complete ecosystem covering the full model lifecycle: optimization, conversion, debugging, and app integration.

- The same framework powering on-device Apple Intelligence — now available to developers
- Uses all of Apple Silicon: CPU, GPU, Neural Engine
- Modern Swift API using non-escapable types for memory safety without sacrificing performance
- Scales from small classification models up to 70B parameter LLMs — all local, zero server cost

---

## Full Workflow

```
PyTorch model (authored/trained)
    ↓
coreai-torch Python package (conversion)
    ↓
Numeric verification (PyTorch output vs Core AI output)
    ↓
Xcode (.aimodel file inspection)
    ↓
Swift Core AI Framework (inference in app)
    ↓
Instruments / Visual Debugger / Debug Gauge (profiling & debugging)
```

---

## Core Types (Swift)

Described verbally in transcript — no verified code available.

| Type | Purpose |
|---|---|
| `AIModel` | Loaded from a `.aimodel` file; used to inspect and load inference functions |
| `InferenceFunction` | The runnable compute graph; call `.run()` to execute inference |
| `NDArray` | Multi-dimensional input/output data container |
| `NDArray.MutableView` | Non-escapable type; safe direct access to NDArray's backing memory |
| `States` | Inputs that are both read and updated in-place during inference (used for KV cache) |

---

## Conversion Flow (Python)

Source: https://apple.github.io/coreai-torch/main/

```python
import torch
from coreai_torch import TorchConverter, get_decomp_table

model = MyModel().eval()
ep = torch.export.export(model, args=(torch.randn(1, 10),))
ep = ep.run_decompositions(get_decomp_table())
coreai_program = TorchConverter().add_exported_program(ep).to_coreai()
coreai_program.optimize()
```

Key API:
- `TorchConverter().add_exported_program(ep)` — add a `torch.export` exported program
- `TorchConverter().add_pytorch_module(model, ...)` — alternatively, add an `nn.Module` directly
- `.to_coreai()` — convert to Core AI format
- `get_decomp_table()` — get the decomposition table for Core AI-compatible ops
- `register_torch_lowering` — register custom lowering functions
- `register_custom_kernels` — register Metal kernel implementations

For the snake game specifically, the speaker added `dynamic_shapes` to `torch.export.export()` so sequence length is not traced as static. States (KV cache) are defined via `torch.register_buffer()` in the PyTorch module and passed via `state_names` during conversion — this is verbal description from transcript, not verified API syntax.

---

## States — KV Cache Optimization

### Problem
Transformer models have quadratic time complexity with respect to sequence length. Without caching, every inference recomputes keys and values for the entire history — latency grows as the sequence grows.

### Solution: States
States are inputs that are **read and updated in-place** during inference. By defining key/value caches as states:
- Model only processes the latest step, not the full history
- Latency stays roughly constant instead of growing

### How to define (Python — conceptual)
Use `torch.register_buffer()` to add key/value cache tensors to the PyTorch module. Core AI will convert these registered buffers into States. During conversion, pass `state_names` to `CoreAIConverter.convert()`.

### How to use (Swift — conceptual)
Initialize NDArrays for the key/value caches at the expected fixed shape (max context length). Pass `mutableView` of these NDArrays as the `states` argument to `inferenceFunction.run()`. The caches are updated in-place on each call.

---

## Model Specialization

`.aimodel` is a portable format that runs on any Apple device. Before running, it must be **specialized** (compiled) for the specific device — expensive the first time, fast from cache after that.

**Key rule: never trigger specialization during user-interactive flows.**

Recommended approach (conceptual, from transcript):
- Check `AIModelCache.default` first — if the model is already cached, load from there
- If not cached, show loading UI or trigger specialization in the background ahead of time
- Use `SpecializationOptions` to configure how the model is optimized
- Optionally share the cache across apps in the same App Group

### Ahead-of-Time (AOT) Compilation
Pre-compile part of the model on the development machine before shipping. The user's device still specializes, but has far less work to do — significantly faster first load.

Reference: [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
Reference: [Managing model specialization and caching](https://developer.apple.com/documentation/CoreAI/managing-model-specialization-and-caching)

---

## Debugging Tools

| Tool | Purpose |
|---|---|
| **Core AI Instruments** | Profile inference latency over time; spot growing latency early |
| **Visual Debugger** | Inspect intermediate tensor values; trace operations back to Python source |
| **Debug Gauge** | Streaming real-time monitor in Xcode sidebar — check this before opening Instruments |

---

## Core AI Models Repository

- Pre-optimized popular models, one command to convert and use
- AI Skills for model authoring, optimization, and conversion
- Swift package with higher-level APIs per model family (low-level inference optimizations built in)
- `CoreAILanguageModel` — plugs directly into Foundation Models framework, lets you use custom models with custom token sampling

---

## Relationship to Foundation Models Framework

Foundation Models is the high-level conversational/agentic API. Core AI is the low-level inference engine underneath. Use `CoreAILanguageModel` to bridge them — bring your own model into Foundation Models' session API.

---

## Resources
- [Core AI documentation](https://developer.apple.com/documentation/CoreAI)
- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [Managing model specialization and caching](https://developer.apple.com/documentation/CoreAI/managing-model-specialization-and-caching)
- Session page: https://developer.apple.com/videos/play/wwdc2026/324/
