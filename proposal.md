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

## Related Session Notes
- `/Meet the Music Understanding framework/CLAUDE.md` — full framework notes
- `/Dive into Core AI model authoring and optimization/CLAUDE.md` — coreai-torch conversion pipeline, coreai-opt, CoreAI Debugger
