# Build a responsive camera app that launches quickly

# WWDC 2026: Build a Responsive Camera App That Launches Quickly

## Session Overview
This session focuses on optimizing camera application performance, specifically targeting launch speed and sustained responsiveness. The presenter, an engineer from the Apple camera performance team, emphasizes that the speed at which the preview frame appears is the most critical factor in user perception. By adopting new APIs and architectural patterns—such as the **Deferred Start** framework—developers can significantly reduce "blank screen" time, minimize system pressure, and ensure high-data-rate video (like ProRes) records smoothly without dropped frames.

---

## Key Concepts, APIs, and Frameworks

### 1. Launch Optimization (Deferred Start)
The session introduces **Deferred Start** (iOS 26+), which allows developers to postpone the initialization of resource-heavy capture outputs until *after* the camera preview is already rendering on screen.
*   **Automatic Mode:** Default for apps recompiled against the iOS 26 SDK. The system determines the optimal time to initialize deferred outputs.
*   **Manual Mode:** Provides finer control, allowing developers to set up specific UI or preferences before triggering the initialization of heavy outputs via `runDeferredStartWhenNeeded()`.

### 2. Sustained Performance
To prevent the app from feeling "laggy" or crashing under heavy thermal load, the session highlights two monitoring APIs:
*   **Hardware Cost API:** Returns a value (0.0 to 1.0) indicating how much of the camera hardware the session is currently using. A value > 1.0 suggests an unsustainable configuration.
*   **System Pressure State:** Monitors the device’s thermal and resource status, allowing the app to dynamically throttle frame rates or reduce GPU/Neural Engine usage.

### 3. High Data Rate Recording
*   **AVProVideoStorage:** A new API (iOS 27) that manages pre-allocated storage for high-data-rate video (ProRes). This prevents non-deterministic file I/O issues (stuttering) by using system-managed, pre-allocated storage space.

---

## Code Patterns & Techniques

### Launch Sequence Separation
*   **Phase 1 (Critical):** Only initialize the preview layer and essential UI (e.g., shutter button).
*   **Phase 2 (Post-Render):** Initialize non-essential UI (mode pickers, image wells) and secondary capture outputs after the preview frame is live.
*   **Threading:** Always dispatch `AVCaptureSession` creation and `startRunning`/`stopRunning` calls off the main thread to prevent UI hangs.

### Implementing Deferred Start
To defer an output, set `isDeferredStartEnabled = true` on the desired `AVCaptureOutput`.
*   **Delegate Callbacks:** Use `sessionWillRunDeferredStart` (to prepare resources) and `sessionDidRunDeferredStart` (to acknowledge that outputs are ready).
*   **Responsive Capture:** To solve the issue where deferring `AVCapturePhotoOutput` delays the first photo, use `isResponsiveCaptureEnabled = true`. This adds a buffer that allows the user to trigger a capture while the output is still initializing.

### Monitoring Performance
```swift
// Example workflow for monitoring
let cost = session.hardwareCost 
if cost > 1.0 { /* Downscale or reduce frame rate */ }

// Register for pressure updates
session.addObserver(self, forKeyPath: "systemPressureState", ...)
```

---

## Practical Takeaways for Developers

*   **Prioritize Preview:** The user’s primary interaction is the preview feed. If the preview is slow, the app feels broken. Always use `AVCaptureVideoPreviewLayer` for simple preview needs, as it is highly optimized for power and latency.
*   **Use `AVCaptureVideoDataOutput` for Control:** Only use this if you need per-frame processing (e.g., custom UI overlays or Metal integration). If you use this, you must adopt **Manual Deferred Start** to match the launch speed of the standard Preview Layer.
*   **Adopt "Pro" Best Practices:** For any high-bitrate recording, always check for support via `isProVideoStorageSupported` and use the `AVProVideoStorage` singleton to ensure deterministic write performance.
*   **Real-World Testing:** Developers should move beyond testing in controlled environments. Use **Xcode Instruments** to measure performance under stress (e.g., high heat) to ensure the app remains responsive in the field.
*   **Refactor for Modern SDKs:** Simply recompiling with the iOS 26+ SDK enables Automatic Deferred Start for apps using `AVCaptureVideoPreviewLayer`, providing an immediate "free" performance win.

<!-- resources -->

---

## Resources

- [Performance and metrics](https://developer.apple.com/documentation/Xcode/performance-and-metrics)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/303/5/fb6dc55a-c026-4ce1-9902-7a744fef4c99/downloads/wwdc2026-303_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/303/5/fb6dc55a-c026-4ce1-9902-7a744fef4c99/downloads/wwdc2026-303_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/303/
