# Dive into Core AI model authoring and optimization — Session Notes

Sources:
- Transcript: full walkthrough (Sachin + Nicole, CoreAI team), using SAM3 as the running example
- Documentation fetches:
  - https://apple.github.io/coreai-torch/main/ — conversion pipeline (verified, has Python code)
  - https://apple.github.io/coreai-torch/main/api/debugging.html — `save_intermediates` API (verified, has Python code)
  - https://developer.apple.com/tutorials/data/documentation/coreai/inspecting-debugging-and-profiling-core-ai-models.json — three-tool overview (verified, no code)
  - https://developer.apple.com/tutorials/data/documentation/coreai/inspecting-core-ai-models-with-core-ai-debugger.json — CoreAI Debugger workflow (verified, no code)
  - https://apple.github.io/coreai-optimization/ and /examples/toy_models.html — index pages only; `QuantizerConfig.presets.w4/w4_per_block/w8`, `KMeansPalettizerConfig`, `KMeansPalettizer`, `ExecutionMode` are real API names referenced in docs, but **no usage code was retrievable**
- Custom Metal kernel registration and model re-authoring (ImageEncode/TextEncode/Detect split) are **transcript-described only** — no doc page covers these; treat code-shaped descriptions as conceptual, not verified syntax.

---

## 1. CoreAI Python Ecosystem

| Component | Role |
|---|---|
| **CoreAI Models Repo** | Open-source model catalog; Swift package for LLMs in-app; reusable components/examples for re-authoring |
| **CoreAI Skills** | Installable into coding assistants — domain-knowledge expert that turns high-level requirements (model, hardware targets, constraints) into a deployment plan (PyTorch changes, conversion, optimization) |
| **CoreAI Torch** (`coreai-torch`, `pip install coreai-torch`) | Converts PyTorch exported programs → `.aimodel` |
| **CoreAI Opt** (`coreai-opt`) | Config-driven compression: quantization (int4/int8/fp4/fp8) and palettization, with data-free / calibration-based / fine-tuning(QAT) workflows |
| **CoreAI Debugger** | Standalone macOS app — visualize model graph, run on-device, validate against reference |

Workflow stays in Python/PyTorch throughout; feels familiar to `coremltools` users.

---

## 2. Core Conversion Pipeline (verified)

```python
import torch
from coreai_torch import TorchConverter, get_decomp_table

model = MyModel().eval()
ep = torch.export.export(model, args=(torch.randn(1, 10),))
ep = ep.run_decompositions(get_decomp_table())
coreai_program = TorchConverter().add_exported_program(ep).to_coreai()
coreai_program.optimize()
```

1. **Export** — `torch.export.export` captures full computational graph (weights, ops, shapes)
2. **Decompose** — `get_decomp_table()` lowers composite ATen ops to primitives while preserving high-level semantics (e.g. attention) that CoreAI recognizes
3. **Convert** — `TorchConverter().add_exported_program(ep).to_coreai()` → `AIProgram`, then `.optimize()` → on-device asset
4. **Inference from Python** — load a function from the program, pass `{input_name: numpy_array}` dict; specialization options can be passed at load time

---

## 3. Case Study: SAM3 Structure

SAM3 (850M params, promptable image segmentation):
- **Image Encoder** + **Text Encoder** — 96% of parameters
- **Detector** (detection transformer + mask decoder) — ~4% of parameters, produces final segmentation mask

This breakdown drives later compression decisions.

---

## 4. CoreAI Opt — First Pass

`coreai-opt` supports:
- Quantization: int4/int8/fp4/fp8, flexible granularity, different schemes per platform (macOS vs iOS)
- Palettization: lookup-table based weight clustering
- Workflows: data-free (seconds–min, 8-bit), calibration-based (~small calibration set, activation quant), fine-tuning/QAT (aggressive 4-bit+)

API names referenced in docs (no verified code): `QuantizerConfig.presets.w4`, `.w4_per_block`, `.w8`; `ModuleQuantizerConfig`; `KMeansPalettizerConfig` (w4/w6/w8 presets); `KMeansPalettizer`; `ExecutionMode`.

**Result of applying `Presets.w4`** (4-bit per-channel symmetric quant, described in transcript as one line) to SAM3:
- Size: 3GB → ~430–530MB
- Quality regression: one occluded flower no longer detected (vs. baseline where all flowers detected)

Problem: can't tell which layer(s) caused the regression from output alone — need to inspect inside the model.

---

## 5. CoreAI Debugger — Diagnosis Workflow (verified from docs)

Standalone macOS app, three-step workflow: **visualize → execute → validate**.

### Workspace
- **Navigator** (left) — operations grouped by PyTorch module
- **Structure Viewer** (center top) — graphical operation graph: connectivity, execution order, data dependencies
- **Source Viewer** (center bottom) — original Python source, line-level (requires debug metadata exported at conversion time)
- **Inspector** (right) — operation description, inputs/outputs, tensor formats; click tensor preview for visualization

### Execute on device
- Click "Device", pick a target (e.g. Mac) in Scheme settings, specify inputs (predefined tensors: zeros/ones/random, or NumPy files)
- Click Run → model specializes for the target → structure viewer shows the as-run model → click any op to see its output tensor

### Validate — against reference run
Use `save_intermediates` (verified API, from `coreai_torch.debugging.torch_utils`):

```python
from coreai_torch.debugging.torch_utils import save_intermediates
from pathlib import Path

exported_program = torch.export.export(model, args=example_input)
metadata_path = save_intermediates(
    program=exported_program,
    inputs=example_input,
    output_dir=Path("./debug_output")
)
# returns path to a .aimodelintermediates file
```

Optional `node_filter` to restrict which ops get saved:

```python
def custom_filter(node, result):
    return any(op in str(node.target).lower()
               for op in ["conv", "linear", "matmul"])

metadata_path = save_intermediates(
    program=exported_program,
    inputs=example_input,
    output_dir=Path("./debug_output"),
    node_filter=custom_filter
)
```

Load the `.aimodelintermediates` file alongside the `.aimodel` in Debugger to start a comparison.

### Validate — across configurations
Compare two runs of the same `.aimodel` (e.g. different hardware target or compute unit) without a PyTorch reference.

### Sync Points & Similarity
- **Sync points** = operation pairs (specialized model output vs. PyTorch reference output) expected to match
- Debugger auto-identifies sync points across the model
- Default similarity metric: **PSNR** (configurable)
- Color coding: green = similar, yellow = moderate divergence, red = significant difference
- Sort by similarity, navigate low-PSNR sync points with arrow keys; source viewer jumps to the originating PyTorch module

### SAM3 Diagnosis Result
Most low-PSNR sync points traced back to the **detector decoder**. Since detector is only ~4% of params, compressing it wasn't worth the quality loss anyway. Fix: re-export with detector excluded from quantization → baseline quality restored (all flowers detected) while keeping the smaller overall size.

### Three Profiling Tools (verified overview, no code)
1. **CoreAI Debugger** — standalone app, structural inspection + validation (download: developer.apple.com/core-ai-debugger)
2. **CoreAI Debug Gauge** — built into Xcode; real-time model load/specialization/inference event tracking
3. **CoreAI Instrument** — Instruments template; CPU/GPU/Neural Engine timing, specialization verification, reload frequency

Workflow: Debug Gauge events → escalate to Debugger for structure → escalate to Instrument for detailed profiling.

---

## 6. Advanced: Custom Metal Kernels (transcript-described, not doc-verified)

CoreAI ships prepackaged optimized kernels for heavy ops (e.g. scaled-dot-product attention) — see CoreAI Models repo for examples.

For further customization, register your own Metal Shading Language (MSL) kernel:
- Define a PyTorch reference implementation of the op (e.g. GELU activation) — this is what `torch.export` traces
- Write an equivalent MSL element-wise kernel computing the fused activation on GPU
- Register a CoreAI Torch Metal kernel: pass the MSL source, the PyTorch reference, and input/output names (e.g. `x`, `y`) — names must match between MSL and registration
- Call it like a normal Python function in the model, passing inputs and specifying the thread grid
- For dynamic-shape models, pass result shapes at each call site so CoreAI can compute output shapes from input shapes
- At conversion time, register the custom kernel(s) with `TorchConverter` before adding the exported program — the MSL source gets embedded directly in the `.aimodel` asset (kernel travels with the model)

See *Optimize Custom Machine Learning Operations with Metal Tensors* session for a full walkthrough with SAM3.

---

## 7. Model Re-authoring (transcript-described, not doc-verified)

For more aggressive optimization (especially iOS), rewrite the PyTorch implementation entirely rather than converting end-to-end.

Common techniques:
- Use recognized PyTorch patterns that map to optimized runtime implementations (e.g. in-place KV-cache updates for LLMs)
- For iOS: static tensor shapes, channels-first tensor layouts, convolutional op patterns — lets CoreAI use native hardware primitives
- Rigorous module-level and model-level testing required after rewriting

### SAM3 Re-authoring Example
Split the model into **three independent functions/modules** instead of one:
- `ImageEncoder` — attention block rewritten using convolutional projections instead of linear layers (lets CoreAI use native hardware primitives); input size reduced 1008px → 336px for iPhone
- `TextEncoder` — similar treatment
- `Detector` — left mostly unchanged (small fraction of compute, low payoff from reauthoring)

Compression: 4-bit palettization with per-channel scales (`KMeansPalettizer`, prepare/finalize flow similar to quantizer) applied to the two encoders; detector left uncompressed (known sensitive to compression from the earlier diagnosis).

All three exported programs cast to half precision, converted via a **single `TorchConverter`** with three separate entry-point names (`image_encode`, `text_encode`, `detect`) → one `.aimodel` asset with three callable functions.

**Payoff**: changing the prompt (e.g. "flower" → "butterfly") only requires re-running TextEncode + Detect, reusing the already-computed image encoding. Result: **76% faster** second inference, even after warm-up.

---

## Summary: What You Can Do Today
1. Convert PyTorch models via CoreAI's Python libraries
2. Optimize with CoreAI Opt (quantization/palettization presets, with care re: sensitive sub-modules)
3. Use CoreAI Debugger to diagnose quality regressions via sync points + PSNR
4. Build on CoreAI Models repo examples for re-authoring patterns
5. Use CoreAI Skills in your AI coding agent for expert-level guidance from the start

---

## Related Sessions
- *Meet Core AI* — design philosophy, high-level API overview
- *Integrate on-device AI models into your app using Core AI* — using SAM3 + Quinn in a real app (Swift side)
- *Optimize Custom Machine Learning Operations with Metal Tensors* — deep dive on custom Metal kernels with SAM3

---

## Resources
- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Inspecting, debugging, and profiling Core AI models](https://developer.apple.com/documentation/CoreAI/inspecting-debugging-and-profiling-core-ai-models)
- [Inspecting Core AI models with Core AI Debugger](https://developer.apple.com/documentation/CoreAI/inspecting-core-ai-models-with-core-ai-debugger)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- Session page: https://developer.apple.com/videos/play/wwdc2026/325/
