# Integrate on-device AI models into your app using Core AI — Session Notes

Sources:
- Transcript: full walkthrough building a language-learning app (vocab cards from camera photos)
- Documentation fetches:
  - https://developer.apple.com/tutorials/data/documentation/coreai.json — Core AI framework overview (types only, no LanguageModelSession/CoreAILanguageModel examples)
  - https://developer.apple.com/tutorials/data/documentation/coreai/compiling-core-ai-models-ahead-of-time.json — AOT compilation (verified)
  - https://apple.github.io/coreai-torch/main/ — coreai-torch conversion workflow (verified, has Python code)
  - https://apple.github.io/coreai-optimization — coreai-opt overview (no code)
  - https://apple.github.io/coreai-torch/main/coreai-core — coreai-core Python package overview (no code)
- `CoreAIImageSegmenter`, `CoreAILanguageModel`, `LanguageModelSession`, `@Generable` usage shown below are **transcript-described only** — not found in fetched docs. Treat as conceptual/structural until verified against sample code.

---

## App Scenario

Language-learning app: user points camera at an object → on-device vision model segments it → on-device LLM generates a vocab card (word, translation, example sentence) in the target language. Fully on-device: no server, no per-token cost, no cloud latency.

---

## 1. Model Decomposition

Avoid "one giant model." Decompose into task-specific models, each under 1B parameters:

| Model | Task |
|---|---|
| **SAM3** (Segment Anything Model 3) | Vision transformer; promptable image segmentation — isolates object from camera frame given a text prompt |
| **Quinn 0.6B** (multilingual LLM) | Reasoning model; 119 languages/dialects; turns an English label into vocab/translation/example sentence |

Benefits: smaller individual models, higher quality per task, independently upgradable.

---

## 2. Getting Models into Core AI Format

Two paths:

### A. Direct PyTorch conversion (`coreai-torch`) — verified from docs

```python
import torch
from coreai_torch import TorchConverter, get_decomp_table

model = MyModel().eval()
ep = torch.export.export(model, args=(torch.randn(1, 10),))
ep = ep.run_decompositions(get_decomp_table())
coreai_program = TorchConverter().add_exported_program(ep).to_coreai()
coreai_program.optimize()
```

- `torch.export.export` → `ExportedProgram` (computation graph)
- `get_decomp_table()` → lowers composite ATen ops to primitives
- `TorchConverter().add_exported_program(ep).to_coreai()` → produces `AIProgram`
- Custom ops: `register_torch_lowering()`; GPU kernels: `TorchMetalKernel` / `register_custom_kernels()`

Optional compression via `coreai-opt` (Core AI Optimization):
- Quantization (INT2–INT8, FP4–FP8), Palettization (1–8 bit lookup tables), Pruning
- Three workflows: data-free (seconds–minutes, 8-bit), calibration-based (~128 samples), fine-tuning (full data, aggressive 4-bit+)

### B. Core AI Models repo (used in this session)

- Catalog of popular models (incl. SAM3, Quinn family) with export recipes + platform-specific variants
- Ships a **Swift runtime package** (Core AI LM, Core AI Segmentation) that wraps pre/post-processing (text encoding, mask extraction/labeling) — no manual tensor wrangling
- Add as Swift package dependency, select the relevant library targets

---

## 3. Integration Code (transcript-described, not doc-verified)

```swift
// Image segmentation
let segmenter = CoreAIImageSegmenter(modelBundle: sam3Bundle)
// load SAM3, run text-prompted segmentation (e.g. "flower"), extract best mask

// Language model — one line to load
let model = CoreAILanguageModel(bundle: quinnBundle)
// abstracts asset loading, engine creation, tokenizer setup

// Same API as Apple's built-in on-device model (FoundationModels framework)
import FoundationModels
let session = LanguageModelSession(model: model)
let response = try await session.respond(to: prompt)
// same streaming support, same structured output
```

**Guided Generation**: use `@Generable` macro to define the vocab card's fields (word, translation, example sentence) so the model returns typed/structured data instead of freeform text.

---

## 4. Model Specialization & Caching

From `coreai.json` (verified types, no usage examples):
- `AIModel` — specialized model ready for on-device inference
- `AIModelAsset` — unspecialized source model asset
- `AIModelCache` — stores specialized artifacts
- `SpecializationOptions` — configuration for specialization

**Specialization** = one-time process that compiles a generic model for the specific device's hardware. Can be very slow for large models. First load = slow (specialization + cache write); subsequent loads = fast (from cache).

**Problem**: triggering specialization during the user's first interactive use (e.g. tapping the camera shutter) causes a stall.

**Solution — First-Run Experience**: don't trigger at app launch or silently in background (wasteful if user never uses the feature). Instead, build a dedicated "feature introduction" screen — specialization happens while the user reads about the feature, not during the interactive flow.

---

## 5. Deployment: Background Assets

Bundling SAM3 + Quinn added >1GB to the app — hits every updater even if they never use the feature.

**Solution**: use **Background Assets** (see "Discover Apple hosted background assets," WWDC25) — only download models when the user opts into the AI feature from the feature-introduction screen, with download progress UI.

---

## 6. AOT (Ahead-of-Time) Compilation — verified from docs

Specialization has two phases:
1. **Compilation** (most expensive) — generic model → executable form
2. **Artifact generation** — tied to specific device/OS version

`coreai-build` lets you run phase 1 on your dev machine ahead of time.

**Requirements**: Metal Toolchain
```shell
xcodebuild -downloadComponent MetalToolchain
```

**Compile**:
```shell
xcrun coreai-build compile MyModel.aimodel --platform iOS --output compiled/
```
- Produces one `.aimodelc` per device architecture: `MyModel.<arch>.aimodelc`
- Override compute units with `--preferred-compute`; see `coreai-build compile --help` for all options

**Load at runtime** — no code changes needed beyond picking the right asset:
```swift
let arch = AIModel.deviceArchitectureName
let assetName = "MyModel.\(arch).aimodelc"
// use standard AIModel(contentsOf:options:)
```

**Device support**: AOT targets Apple Intelligence-capable devices only (A17 Pro+ iPhone/iPad, M1+ Mac, M2+ Vision Pro). Even with AOT, some on-device specialization still occurs — just much less.

**Deployment pattern**: build `.aimodelc` per architecture → distribute each as a separate Background Asset → app detects device architecture and requests the matching asset.

---

## 7. Multi-Platform: iOS → macOS

Same code, reused on Mac with more headroom:

- **Swap to larger model variant**: Quinn 0.6B (iPhone) → Quinn 8B (Mac) — better reasoning, higher quality (e.g. richer prompts, multiple example sentences, pinyin generation)
- **Batch processing layer**: process a whole folder of photos at once — parallelize segmentation across all photos, then run generation across all results
- **Longer context → new capabilities**: feed the model a whole category of vocab and have it produce a structured curriculum — sequence simple→complex, group into lessons, reuse earlier vocab in new example sentences (one prompt → full lesson plan)

Same `LanguageModelSession` API throughout — only the underlying model and surrounding orchestration code differ.

---

## 8. Core AI Instruments

New Xcode instrument traces model load, specialization, and inference timing — used in the session to diagnose a UI stall as a specialization event during first model load.

---

## Related Sessions
- *Meet Core AI* — design philosophy, high-level API overview
- *Dive into Core AI model authoring and optimization* — model conversion/optimization details, including SAM3 conversion walkthrough
- *Compiling Core AI models ahead of time* (doc article) — AOT details

---

## Resources
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- Session page: https://developer.apple.com/videos/play/wwdc2026/326/
