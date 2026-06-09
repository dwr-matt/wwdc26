# Implement high resolution photo capture

# WWDC 2026: Implement High-Resolution Photo Capture

## Session Overview
This session explores the technical challenges and best practices for implementing high-resolution photo capture (up to 48MP) on modern iPhone hardware. The speaker, Mohit Sethia, details how to balance image quality against processing overhead, ensuring that apps remain responsive and capable of capturing high-fidelity photos without blocking the user interface or missing critical moments.

---

## Key Concepts and APIs

### Resolution Options
*   **12MP:** Standard high-resolution capture.
*   **24MP:** Introduced with iPhone 15; uses a combination of quad-sensor multi-frame fusion (12MP) and 48MP detail data via the Photonic Engine.
*   **48MP:** Captures full sensor resolution, ideal for heavy cropping and detail analysis. 

### Quality Prioritization Levels
Developers can configure the `AVCapturePhotoOutput` using `maxPhotoQualityPrioritization`:
*   **Speed:** Lowest latency, least processing, fastest delivery.
*   **Balanced:** Medium latency, optimized quality.
*   **Quality:** Longest processing time, maximum image fidelity (required for 24MP/48MP multi-frame fusion).

### Responsiveness APIs
*   **`AVCapturePhotoOutput.isResponsiveCaptureEnabled`:** Allows the session to begin the next capture as soon as the current *capture* stage finishes, without waiting for the *processing* stage to complete.
*   **`isFastCapturePrioritizationEnabled`:** Dynamically adjusts the quality from "Quality" to "Balanced" during rapid-fire shooting to prevent the system from becoming blocked.
*   **Deferred Photo Processing:** Transfers final image processing to the background or "on-demand," returning a proxy image immediately to keep the UI responsive.

---

## Recommended Code Patterns

### 1. Pre-allocating Resources
To prevent stutter or lag when a user initiates a capture, developers should use `setPreparePhotoSettingsArray` as early as possible.
*   **Pattern:** Create a "prepare" settings object matching your desired dimensions and quality, and pass it to the output.
*   **Constraint:** You cannot reuse the exact same settings object for the actual capture; you must create a new object that matches the pre-allocated configuration.

### 2. Monitoring Progress
Use the `AVCapturePhotoCaptureDelegate` to track the state:
*   **`didCapturePhoto` / `didFinishCapture`:** Monitor these to understand when the hardware stage is complete.
*   **`photoProcessingTimeRange`:** Use this property on `AVCaptureResolvedPhotoSettings` to manage user expectations regarding delivery time.

### 3. Configuring Sessions
*   Always use the `.photo` preset.
*   Check `supportedMaxPhotoDimensions` on the active device format before setting values.
*   Configure the session **before** committing; changing these settings after the session starts triggers a costly pipeline reconfiguration.

---

## Practical Takeaways for Developers

1.  **Don't Over-Configure:** Choose resolutions based on the app’s specific use case. If you need 48MP for analysis, use it, but be aware of the memory and processing requirements.
2.  **Responsiveness is Paramount:** For dynamic scenarios (e.g., sports, street photography), enable both `isResponsiveCaptureEnabled` and `isFastCapturePrioritizationEnabled`. These allow the app to pivot from high-quality mode to balanced mode automatically during rapid bursts, preventing the shutter button from locking.
3.  **Adopt Deferred Processing:** This is the most effective way to eliminate shot-to-shot delay. It allows the user to continue shooting while the system handles compute-intensive Photonic Engine tasks in the background.
4.  **Avoid Pipeline Reconfiguration:** Set your `maxPhotoDimensions` and `photoQualityPrioritization` once at initialization or upon camera mode changes to keep the capture pipeline stable.
5.  **Observe Readiness:** Use the `isCaptureReady` property on `AVCapturePhotoOutput` to provide UI feedback to the user on when they can take the next photo.

<!-- resources -->

---

## Resources

- [Capturing photos in RAW and Apple ProRAW formats](https://developer.apple.com/documentation/AVFoundation/capturing-photos-in-raw-and-apple-proraw-formats)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/304/4/7a18d6ee-a63d-4402-bfb6-85a21dfac7dd/downloads/wwdc2026-304_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/304/4/7a18d6ee-a63d-4402-bfb6-85a21dfac7dd/downloads/wwdc2026-304_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/304/
