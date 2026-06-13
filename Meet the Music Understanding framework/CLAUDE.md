# Meet the Music Understanding framework — Session Notes

Sources:
- Transcript: full walkthrough (Connor, Computational Music team), demoing "Music Understanding Lab" sample app, framework powers Final Cut Pro's Beat Detection and Montage features
- Documentation fetches:
  - https://developer.apple.com/tutorials/data/documentation/musicunderstanding.json — verified, confirms `MusicUnderstandingSession` is an actor, lists result types (`SessionResult`, `RhythmResult`, `KeyResult`, `LoudnessResult`, `PaceResult`, `StructureResult`, `InstrumentActivityResult`) and support types (`TimedValue`, `RangedValue`, `AnalysisType`, `MusicUnderstandingError`); has 2 verified Swift signatures (below)
  - https://developer.apple.com/tutorials/data/documentation/musicunderstanding/create-visuals-using-musicunderstanding-analysis-results.json — verified, overview of "Music Understanding Lab" sample project, no inline code
- Result struct field-level details (RhythmResult.beats/bars, StructureResult sections/segments/phrases, KeyResult tonic/mode, PaceResult events-per-minute, InstrumentActivityResult ranges/activity, LoudnessResult integrated/momentary/short-term/peak), the streaming-loudness dual-task pattern, custom audio provider pattern, JSON export via Codable, and the video-sync-to-music algorithm are **transcript-described only** — no Swift code retrievable from fetched docs. Treat as conceptual/structural.
- Naming discrepancy: README/transcript refer to custom audio provider buffers as `AVAudioPCMBuffer`; the verified doc (musicunderstanding.json) names the type `AVReadOnlyAudioPCMBuffer`. Prefer the doc-verified name.

---

## 1. What It Is / Why It Exists

On-device, private, offline musical intelligence framework (iOS/iPadOS/macOS/tvOS/visionOS/watchOS 27.0+, all beta). Powers two Final Cut Pro features:
- **Beat Detection** — uses Rhythm + Structure results to build a beat grid
- **Montage** — uses Rhythm + Pace + Structure to auto-sync video clip timing to music

---

## 2. Core Session API (verified types, partial verified code)

`MusicUnderstandingSession` — an **actor** (verified from musicunderstanding.json).

Verified signatures:
```swift
// Incremental loudness results (async sequence)
var loudnessResults: some Sendable & AsyncSequence<LoudnessResult, any Error>

// Cancel ongoing analysis
func cancel() async
```

Input options (transcript-described, type names partially verified):
- File-based: `AVAsset`, recommend `preferPreciseDurationAndTiming = true` for accurate timing
- Stream-based: custom `AsyncSequence` audio provider yielding PCM buffers — doc names the buffer type `AVReadOnlyAudioPCMBuffer` (transcript said `AVAudioPCMBuffer`); sequence ends by yielding `nil`

`analyze()` runs all six analysis domains by default, or can be scoped via `AnalysisType` (verified type name exists, case names not verified). Returns `SessionResult` (verified type name) — each domain's result is an optional field, nil if not requested/analyzed.

---

## 3. Time-Association Support Types (verified type names)

- **`TimedValue<T>`** — a value paired with a `CMTime` (point in time)
- **`RangedValue<T>`** — a value paired with a `CMTimeRange` (time span)

---

## 4. The Six Analysis Domains (transcript-described field details)

Result type names verified (musicunderstanding.json); internal field semantics below are transcript-only.

1. **RhythmResult** — `beats: [CMTime]`, `bars: [CMTime]`; optional global BPM (nil if fewer than 2 beats detected)
2. **StructureResult** — three-level hierarchy: `sections`, `segments`, `phrases`, each `[CMTimeRange]`
3. **KeyResult** — `[RangedValue]` mapping key signature (tonic + mode: major/minor) to a `CMTimeRange` (supports key changes mid-track)
4. **PaceResult** — single property, `[RangedValue]` of events-per-minute (rhythmic density over time)
5. **InstrumentActivityResult** — two properties: `ranges` (dict instrument name → `[CMTimeRange]`, when each instrument is active) and `activity` (dict instrument name → `TimedValue<Float>`, activity level 0.0–1.0 over time)
6. **LoudnessResult** — LUFS / ITU-R BS.1770 standard:
   - `integrated` — single value for whole track
   - `momentary` — every 100ms, 400ms window
   - `short-term` — every 100ms, 3s window
   - `peak` — dB

---

## 5. Streaming Loudness Pattern (transcript-described)

Two concurrent tasks: one consumes the verified `loudnessResults` AsyncSequence for real-time UI updates; the other calls `analyze()` to run the full analysis. No verified code for this pattern.

---

## 6. Serialization (transcript-described)

All result types conform to `Codable`; encode to JSON via `JSONEncoder`. No verified code example.

---

## 7. Video-Sync-to-Music Algorithm (transcript-described, conceptual)

Used in Final Cut Pro Montage:
1. Get section time ranges from `StructureResult`
2. Get pace (events/min ÷ 60 → events/sec) per section from `PaceResult`
3. Derive target clip duration per section from pace — high-pace sections get shorter/faster clips, low-pace sections get longer/slower clips
4. Re-time video clips so visual rhythm aligns with musical rhythm

---

## 8. Sample App

"Music Understanding Lab" — multiplatform (macOS/iOS/iPadOS/visionOS, Xcode 27.0+ beta), built with SwiftUI + Swift Charts. Visualizes Song Structure, Pace, Instrument Activity, Key, BPM, and Loudness over time. Doc provides overview + download link only, no inline code.

---

## Related Sessions
- Sessions using Beat Detection / Montage in Final Cut Pro (not covered here)

---

## Resources
- [Music Understanding](https://developer.apple.com/documentation/MusicUnderstanding)
- [Create visuals using Music Understanding analysis results](https://developer.apple.com/documentation/MusicUnderstanding/create-visuals-using-musicunderstanding-analysis-results)
- Session page: https://developer.apple.com/videos/play/wwdc2026/253/
