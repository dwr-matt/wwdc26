# Meet the Music Understanding framework

# WWDC 2026: Meet the Music Understanding Framework

## Session Overview
This session introduces the **Music Understanding** framework, an Apple-developed toolset designed to provide on-device, private, and offline musical intelligence to developers. By handling complex signal processing and machine learning inference internally, the framework allows apps to analyze audio for key, rhythm, structure, pace, instrument activity, and loudness without requiring domain expertise. The framework powers professional features in Apple’s own apps, such as Beat Detection and Montage in Final Cut Pro.

---

## Key Concepts & Capabilities
The framework provides deep insights into audio files across six primary domains:

*   **Rhythm:** Analyzes the pulse of the music, including individual beats, bars, BPM (Beats Per Minute), phrases, and segments.
*   **Structure:** Maps the hierarchy of a song into sections (e.g., Intro, Verse, Chorus, Bridge), segments, and phrases using `CMTimeRange` objects.
*   **Key:** Identifies the musical key (tonic and mode) and how it shifts throughout the track.
*   **Pace:** Measures the perceived energy or "speed" of the music, allowing developers to correlate audio intensity with user experience.
*   **Instrument Activity:** Detects the presence and intensity of specific instruments (drums, bass, vocals), providing both time-range markers and granular activity graphs (0.0 to 1.0).
*   **Loudness:** Provides industry-standard measurements (LUFS) for integrated, momentary, and short-term loudness, as well as absolute peak values in decibels.

---

## APIs and Implementation Patterns
The framework is designed to be developer-friendly, utilizing standard Swift concurrency and AVFoundation types.

### 1. Basic Session Workflow
Apps interact with the framework via a `MusicUnderstandingSession`:
1.  **Initialize:** Create a session using an `AVAsset` (set `preferPreciseDurationAndTiming = true`) or a custom audio provider.
2.  **Analyze:** Call the `analyze()` method. By default, it runs all analysis; however, for better performance, you can pass specific analysis types to compute only what is needed.
3.  **Handle Results:** Results are returned as structs, where each field (e.g., `keyResult`, `rhythmResult`) is optional and only populated if requested or detected.

### 2. Time Association
The framework relies heavily on:
*   **`TimedValue`:** Associates a value with a `CMTime`.
*   **`RangedValue`:** Associates a value with a `CMTimeRange`.

### 3. Streaming & Custom Sources
*   **Loudness Streaming:** You can use an `AsyncSequence` to receive loudness updates every 100ms for real-time audio-reactive UI.
*   **Custom Audio Providers:** By conforming to `AsyncSequence` and yielding `AVAudioPCMBuffer` objects, you can pipe live or custom audio streams into the analyzer.

### 4. Serialization
All analysis results conform to `Codable`. Developers can easily export the full analysis of a song to a JSON file, which is useful for bundling pre-computed metadata with an app or sharing analysis between different application components.

---

## Practical Takeaways for Developers
*   **Performance Optimization:** Always prefer targeted analysis (requesting only the data you need) rather than the default "analyze all" call to minimize battery and CPU usage.
*   **Visual Synchronization:** Use the `structure` and `pace` data to automate video editing or clip re-timing. For example, calculate clip duration by dividing 60 seconds by the BPM or pace values to ensure video cuts match the energy of the musical section.
*   **Audio-Reactive UI:** Leverage the `instrumentActivity` (activity graph) to drive animations. Because it provides values from 0 to 1, it is ideal for animating elements like frequency bars or visual effects that react to specific instrument tracks.
*   **Developer Resources:** Download the **"Music Understanding Lab"** sample app from the Apple Developer website to experiment with the APIs and visualize how the raw data translates to UI elements.
*   **Privacy-First:** Because all processing happens on-device, you can build sophisticated music analysis features without worrying about server-side audio processing or user data privacy concerns.

<!-- resources -->

---

## Resources

- [Creating visuals with Music Understanding analysis results](https://developer.apple.com/documentation/MusicUnderstanding/create-visuals-using-musicunderstanding-analysis-results)
- [MusicUnderstanding](https://developer.apple.com/documentation/MusicUnderstanding)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/253/5/db1c3715-aaaf-42db-8e9e-66d2a0011430/downloads/wwdc2026-253_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/253/5/db1c3715-aaaf-42db-8e9e-66d2a0011430/downloads/wwdc2026-253_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/253/
