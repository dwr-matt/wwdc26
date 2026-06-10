# Support the Center Stage front camera in your iOS app

# WWDC 2026: Support the Center Stage Front Camera in Your iOS App

## Overview
This session introduces the new Center Stage front camera hardware found on the iPhone 17, iPhone Air, and iPhone 17 Pro. Unlike traditional sensors with fixed 4:3 aspect ratios, this new hardware features a square sensor and a 95-degree ultra-wide field of view. These physical improvements allow developers to offer users flexible framing, automatic zoom, and intelligent rotation without moving the device. The session covers how to integrate these capabilities into photo, video, and video-conferencing applications using the AVFoundation framework.

---

## Key Concepts & APIs
*   **Square Image Sensor:** Allows for cropping different aspect ratios (3x4, 4x3, 9x16, 16x9, 1x1) natively from the sensor without physically rotating the device.
*   **Dynamic Aspect Ratio:** An `AVCaptureDevice` property that enables seamless switching between aspect ratios for both stills and video.
*   **Smart Framing Monitor (`AVCaptureSmartFramingMonitor`):** A new API that provides periodic framing recommendations (zoom/rotation) based on automatic face and gaze detection.
*   **Sensor Orientation Compensation:** A feature that automatically rotates photos to "landscape left" (to match previous iPhone behavior), now controllable via the `cameraSensorOrientationCompensationEnabled` property.
*   **Low-Latency Stabilization:** A new video stabilization mode designed for video calls to minimize handshake during movement.

---

## Code Patterns & Techniques
### 1. Implementing Dynamic Aspect Ratio
To enable "tap-to-rotate" features, developers must:
1.  Discover the Ultra-Wide front camera using `AVCaptureDevice.DiscoverySession`.
2.  Check for supported aspect ratios via the device format's `supportedDynamicAspectRatios`.
3.  Lock the device for configuration and set the active format.
4.  Update the `dynamicAspectRatio` property.

### 2. Utilizing Smart Framing
To implement auto-zoom/auto-rotate for group selfies:
1.  Instantiate the `AVCaptureSmartFramingMonitor` from the `AVCaptureDevice`.
2.  Set `enabledFramings` to the desired modes (e.g., narrow/wide).
3.  Use **Key-Value Observation (KVO)** on the `recommendedFraming` property.
4.  Apply recommendations by updating the `dynamicAspectRatio` and `videoZoomFactor` sequentially (aspect ratio first for smooth transitions).

### 3. Handling Video Calls
*   **Cooperative Mode:** Set the camera to `cooperative` mode to allow the user to control Center Stage both via your app UI and the system-wide Control Center.
*   **Low-Latency Stabilization:** Enable this by setting `preferredVideoStabilizationMode` to `.lowLatency` on the `AVCaptureConnection`.

---

## Practical Takeaways for Developers
*   **Default Compatibility:** The system handles sensor orientation compensation automatically for HEIC, JPEG, and processed photos. For best performance, consider toggling this off if you handle rotation logic manually, but be aware this does not apply to RAW/ProRAW formats.
*   **Video Recording Constraints:** Changing the `Dynamic Aspect Ratio` during a `AVCaptureMovieFileOutput` recording will force the recording to stop because QuickTime tracks require consistent dimensions.
*   **Performance Optimization:** Always test the `4032` resolution format for the highest fidelity photos. When using `AVCaptureSmartFramingMonitor`, note that it is optimized for this specific high-resolution format.
*   **Transitioning from iPad:** If you have previously implemented Center Stage on iPad, the logic is similar, but you should review the new `AVCaptureDevice` properties introduced in iOS 26 to leverage the iPhone-specific square sensor improvements.
*   **Integration:** Use the `AVCaptureVideoDataOutput` if building custom video conferencing solutions to ensure you have direct access to the stabilized, centered stream.

<!-- resources -->

---

## Resources

- [Supporting Center Stage front camera in your iOS app](https://developer.apple.com/documentation/AVFoundation/supporting-center-stage-front-camera-in-your-ios-app)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [AVFoundation](https://developer.apple.com/documentation/AVFoundation)
- [Capture setup](https://developer.apple.com/documentation/AVFoundation/capture-setup)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/341/4/fa1380a3-e2ab-4442-9302-817be212e991/downloads/wwdc2026-341_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/341/4/fa1380a3-e2ab-4442-9302-817be212e991/downloads/wwdc2026-341_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/341/
