# Proposal: WWDC26 Features for Our Apps

Tracking ideas from WWDC26 sessions that could be implemented in our apps. Updated as we study more sessions.

---

## App: haptik (Watch Haptic Metronome)

Current app: silent haptic metronome for Apple Watch, BPM via Shazam integration, multi-device sync (haptik-link), Ableton Link support, recording with iCloud sync.

### From: Meet the Music Understanding framework (session #253)

**Framework**: `MusicUnderstandingSession` (actor), on-device music analysis — iOS/iPadOS/macOS/tvOS/visionOS/watchOS 27.0+ (beta).

#### 1. On-device BPM/beat detection (replaces Shazam dependency)
- `RhythmResult` gives `beats`/`bars` as `[CMTime]` plus optional global BPM, computed entirely on-device from any audio file/stream — no network call, no dependency on the song being in Shazam's database.
- **Impact**: works for any user audio (demos, original tracks, live recordings), fully offline.

#### 2. Real beat-grid alignment (vs. fixed-interval metronome)
- Instead of computing tap intervals from a single BPM number, use the actual `[CMTime]` beat positions from `RhythmResult`.
- **Impact**: handles tempo drift, swing, and rubato — haptic pulses track the real performance instead of a mathematical approximation.

#### 3. Live audio analysis via custom AsyncSequence provider
- Framework accepts a custom `AsyncSequence<AVReadOnlyAudioPCMBuffer>` audio provider (mic input) instead of just `AVAsset` files.
- **Impact**: potential "tap along to a live band" mode — analyze a live room/rehearsal and derive a beat grid in real time, feeding haptik-link sync across multiple watches.

#### 4. Loudness-reactive haptic intensity
- `loudnessResults` is a verified streaming `AsyncSequence<LoudnessResult, any Error>` (LUFS / ITU-R BS.1770).
- **Impact**: scale haptic pulse strength with momentary/short-term loudness — louder sections feel stronger, quiet sections feel subtler.

#### 5. Structure-aware mode switching
- `StructureResult` provides sections/segments/phrases as `[CMTimeRange]` (transcript-described, not doc-verified field shapes).
- **Impact**: detect section changes (e.g. entering chorus) and switch haptic pattern or notify the player — useful during rehearsal.

### Proposed Architecture
- Run `MusicUnderstandingSession` analysis on **iPhone** (avoids unverified watchOS performance/battery cost for on-device ML inference).
- Transfer derived beat timestamps / loudness stream to **Apple Watch** via Watch Connectivity.
- Watch app stays lightweight: just triggers Taptic Engine at received timestamps — minimizes battery drain.
- Live-mic / on-Watch analysis is a stretch goal pending a proof-of-concept on watchOS 27 beta (no documented data on watchOS-side performance for this framework).

### Open Questions / Risks
- Minimum OS bump to 27.0 (iOS/watchOS) — beta only at time of writing, need to assess adoption timeline for users.
- `RhythmResult` global BPM is nil if fewer than 2 beats detected — need fallback behavior (e.g. keep manual BPM entry / Shazam as fallback).
- `AVReadOnlyAudioPCMBuffer` vs `AVAudioPCMBuffer` naming discrepancy between docs and transcript — confirm exact API when beta SDK is available.

---

## Apps: Noted & Capy (AI transcription/meeting apps)

Current diarization stack: **FluidAudio** (pyannote segmentation + WeSpeaker/ECAPA-TDNN embeddings, already converted to CoreML, runs on ANE). Already fully on-device/offline. Benchmarks: 17.7% DER on AMI, 141.2x RTFx on M1, ~100MB models. Also supports cross-recording speaker identification already — no gap there.

### CoreAI vs. FluidAudio (CoreML) for diarization — analysis

**Keep FluidAudio for now.** It's mature, validated, already on-device. No current pain point that CoreAI would solve.

**Pros of FluidAudio (status quo)**:
- Proven, benchmarked, maintained by a third party — zero conversion/optimization work for us
- Swift API, no ML pipeline to maintain
- Multiple model options available (modular pyannote+wespeaker, LS-EEND, Sortformer)

**Cons of FluidAudio**:
- Third-party dependency — no control over update cadence or model choice
- Can't fine-tune on our own meeting data and redeploy (we get pre-converted models only)
- CoreML/ANE path doesn't benefit from M5 Neuron Accelerator (separate hardware path via Tensor Ops/GPU)

**When CoreAI becomes worth it**: only if/when we need to **fine-tune** pyannote/WeSpeaker (or a successor model) on our own data — e.g. to fix accuracy issues with specific accents or multilingual meetings. CoreAI's `torch.export → TorchConverter → optimize` pipeline + `coreai-opt` + CoreAI Debugger would let us deploy and validate a custom-trained checkpoint, which FluidAudio's pre-converted models can't do.

**Cons of switching to CoreAI now**:
- Re-doing conversion/optimization work FluidAudio already validated, with no current quality benchmark of our own
- CoreAI requires OS 27.0+ (beta) — bigger adoption-floor jump than current FluidAudio/CoreML requirements
- Shifts full maintenance burden (model updates, quantization, debugging) onto us

**Decision**: revisit only when a concrete fine-tuning need emerges for diarization accuracy (e.g. specific accent/language complaints from Noted/Capy users).

### Other audio DL model directions for Noted/Capy (not yet covered by current stack)
- Language identification — auto-detect spoken language per segment, big win for Capy's translation UX
- DL-based speech enhancement/denoising — upgrade from current noise reduction
- DL-based VAD — improve "skip silence" accuracy in Noted's smart playback
- Sentence/topic segmentation — punctuation restoration + topic boundaries to improve AI summary grouping
- Audio event detection — flag laughter/applause/etc. as meeting highlights

---

## AskAI Feature (Noted & Capy) — Foundation Models iOS 27 Upgrades

Current state: AskAI uses on-device Foundation Models for free users, Gemini API for paid users (two separate integration paths).

### 1. Wrap Gemini as a `LanguageModel` provider (foundational change)
- iOS 27 opens Foundation Models to third-party providers via `LanguageModel` + `LanguageModelExecutor` protocols (session: "Bring an LLM Provider to the Foundation Models Framework").
- Implement `GeminiLanguageModel: LanguageModel` (executor handles Gemini API calls, transcript mapping, streaming via `LanguageModelExecutorGenerationChannel`).
- **Impact**: AskAI's prompt assembly, tool definitions, `@Generable` structured output, and error handling become shared code across on-device/PCC/Gemini — free vs. paid becomes "swap the model," not two parallel implementations. API keys via Keychain, not hardcoded.

### 2. Dynamic Profiles for free/paid tier switching
- `LanguageModelSession.DynamicProfile` body re-evaluates per prompt — subscription status changes apply on the next prompt without rebuilding the session.
```swift
struct AskAIProfile: LanguageModelSession.DynamicProfile {
    var isPaidUser: Bool
    var body: some LanguageModelSession.DynamicProfile {
        if isPaidUser {
            Profile { AskAIInstructions() }.model(GeminiLanguageModel()).temperature(0.7)
        } else {
            Profile { AskAIInstructions() }.model(SystemLanguageModel())
        }
    }
}
```

### 3. Tool Calling + RAG over user's own transcripts (biggest product win for Noted/Capy)
- Define a custom `Tool` (e.g. `SearchTranscriptsTool`) that queries the app's own transcript/notes database, or use the built-in `SpotlightSearchTool()` if transcripts are Spotlight-indexed.
- **Impact**: AskAI becomes "ask questions about your own meetings/notes" (RAG-grounded) instead of generic LLM chat — direct product differentiation leveraging data Noted/Capy already have.

### 4. PrivateCloudComputeLanguageModel as Gemini alternative/complement
- `PrivateCloudComputeLanguageModel`: 32K context (vs. 4K on-device), multi-level reasoning (.light/.moderate/.deep), Apple privacy story, one-line model swap.
- **Constraints**: requires managed entitlement application; **eligibility limited to apps with <2M downloads** — verify Noted/Capy qualify. Daily quota requires persistent (non-dismissible) quota UI per Apple's guidance.
- **Impact**: potential cost reduction vs. Gemini for paid tier on large-context tasks (e.g. long meeting summarization), with an "Apple-native privacy" marketing angle.

### 5. Multimodal input via `Attachment`
- `Attachment(image)` passes `CGImage`/`CIImage`/`CVPixelBuffer`/URLs directly into prompts, no preprocessing.
- **Impact**: if Capy/Noted notes include screenshots or handwritten photos, AskAI could answer questions about visual content directly.

### Suggested Priority
1. #1 (unify via `LanguageModel` protocol) — foundation for everything else
2. #3 (Tool Calling + own-data RAG) — most direct product differentiation
3. #2 (Dynamic Profiles) — natural consequence of #1
4. #4 (PCC) and #5 (multimodal) — evaluate based on eligibility/need

---

## On-Device-Only Feature Backlog (Noted) — Gated on iOS 27 Local Model Quality

These are LLM-powered note features we've deliberately **not** built on the cloud LLM API path, for two reasons:
- **Privacy**: running these over every note/notebook means sending substantial user content to a cloud API continuously — not acceptable as a default/always-on behavior
- **Cost**: these are "ambient"/automatic features (run on every note, not user-initiated chat) — token cost on a cloud API scales with usage in a way that doesn't work for a flat-rate or free tier

iOS 27's upgraded on-device `SystemLanguageModel` (Foundation Models framework) makes these newly viable as **always-on, free, private, local** features — no per-token cost, no data leaves the device. Revisit once we've evaluated on-device model quality (via the Evaluations framework) for each task.

Candidate features:
1. **Auto-tagging** — suggest tags for a note based on its content
2. **Suggested titles** — generate a title from note content
3. **Notebook organization** — re-cluster/organize notes within a single notebook that's grown too large
4. **Auto-summary / TL;DR** — per-note summary generated automatically
5. **Action item extraction** — pull out to-dos/owners/deadlines from meeting notes
9. **Cross-note Q&A** — ask questions across notes in a notebook (local RAG, on-device)
11. **Related notes suggestions** — surface notes related to the one being viewed (backlink-style)
12. **Follow-up message drafts** — generate a follow-up email/message from meeting notes
14. **Weekly digest** — periodic summary of notes created + open action items

### Notes
- All of these should run via `SystemLanguageModel` (4K context, on-device, unlimited usage, offline) — not PCC or Gemini, to preserve the "free + private + ambient" property.
- 4K context may be limiting for #3/#9/#14 (whole-notebook or cross-note operations) — may need chunking/iterative summarization strategies.
- Use the Evaluations framework to benchmark on-device model quality per task before enabling by default.

---

## Related Session Notes
- `/Meet the Music Understanding framework/CLAUDE.md` — full framework notes
- `/Dive into Core AI model authoring and optimization/CLAUDE.md` — coreai-torch conversion pipeline, coreai-opt, CoreAI Debugger
- `/Bring an LLM provider to the Foundation Models framework/CLAUDE.md` — `LanguageModel`/`LanguageModelExecutor` protocols
- `/Build agentic app experiences with the Foundation Models framework/CLAUDE.md` — Dynamic Profiles, Tool Calling
- `/Build with the new Apple Foundation Model on Private Cloud Compute/CLAUDE.md` — PCC details, eligibility, quota UI
- `/What's new in the Foundation Models framework/CLAUDE.md` — multimodal `Attachment`, overview
