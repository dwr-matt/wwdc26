# Use foveated streaming to bring immersive content to visionOS

# Summary: Use Foveated Streaming to Bring Immersive Content to visionOS

## Session Overview
This session introduces **Foveated Streaming**, a powerful feature introduced in visionOS 26.4 that enables developers to stream high-fidelity, OpenXR-based immersive content from external devices (like PCs or cloud servers) to Apple Vision Pro. By leveraging the eye-tracking capabilities of the headset, the system intelligently compresses the video stream, prioritizing detail where the user is looking. The framework is designed to bridge the gap between existing OpenXR applications and the spatial computing capabilities of visionOS, allowing developers to build hybrid experiences that combine external compute power with native Apple frameworks like ARKit, SwiftUI, and RealityKit.

---

## Key Concepts, APIs, and Frameworks

### Core Technologies
*   **Foveated Streaming Framework:** The primary API on visionOS used to manage connections to streaming endpoints.
*   **NVIDIA CloudXR SDK:** The underlying engine for high-performance streaming. It provides the OpenXR runtime for Windows and handles low-latency video/audio transport, including input pass-through (hands, controllers, mic).
*   **OpenXR:** The industry standard for VR/AR, utilized by the host endpoint to render the application.

### Integration Frameworks
*   **SwiftUI:** Used for the visionOS receiver app's user interface, including windows, volumetric windows, and immersive spaces.
*   **ARKit:** Used to map the physical environment, such as aligning a virtual cockpit with a physical racing wheel or flight simulator.
*   **RealityKit:** Enables native on-device rendering, allowing developers to overlay virtual objects on top of the streamed content with proper occlusion.

---

## Code Patterns and Techniques

### Communication and Pairing
*   **Message Channels:** A bidirectional, opaque data pipe. The visionOS app and the OpenXR client use these channels to exchange custom data, such as loading progress, level selections, or ARKit spatial alignment data.
*   **Pairing Protocol:** A JSON-encoded, TCP-based protocol is used for secure authentication. It utilizes a **Request/Acknowledge** pattern. The endpoint provides a pairing barcode (containing a client token and a certificate hash) which the user scans using the Apple Vision Pro.
*   **Service Discovery:** Endpoints are discovered on the local network using **Bonjour**.

### Rendering and Experience
*   **Progressive Immersion:** A recommended style that lets users keep a "portal" into their real-world environment while viewing the streamed simulation.
*   **Depth Buffering:** Developers should provide an alpha channel and a depth buffer. This allows the system to correctly blend streamed content with the physical environment and native RealityKit assets, enabling features like proper occlusion.
*   **Session Management:** The app must monitor the streaming session state. When a user takes off the headset, the session enters a "paused" state; the endpoint must remain ready to resume immediately once the device is worn again.

---

## Practical Takeaways for Developers

1.  **Fast Time-to-Market:** Apple provides a reference implementation on GitHub for both the Windows host and the visionOS receiver. Developers can generally achieve a basic streaming connection in an afternoon and add visionOS-specific enhancements within a week.
2.  **Hybrid Architecture:** Do not just port an existing app; enhance it. Use **ARKit** to ground the experience in the user's room and **SwiftUI** to build comfortable, system-standard interfaces (menus, pause buttons) that exist outside the stream.
3.  **Performance Monitoring:** Use the **Foveated Streaming Instrument** in Xcode. This tool is essential for tracking bandwidth, pose latency, and frame rates to ensure a smooth, comfortable user experience.
4.  **Hardware Requirements:** The system supports both local PC networks and cloud-based streaming. Because it uses NVIDIA CloudXR, the protocol is optimized for Wi-Fi, enabling high-performance, tetherless experiences.
5.  **Resources:** 
    *   Visit the [Apple Developer website](https://developer.apple.com) for the protocol specification and integration articles.
    *   Download the official **Foveated Streaming Sample App** from GitHub to see the JSON message structure and session handling in practice.

<!-- resources -->

---

## Resources

- [Analyzing the performance of a foveated streaming session](https://developer.apple.com/documentation/FoveatedStreaming/analyzing-the-performance-of-a-foveated-streaming-session)
- [Establishing foveated streaming sessions with Apple Vision Pro](https://developer.apple.com/documentation/FoveatedStreaming/establishing-foveated-streaming-sessions-with-apple-vision-pro)
- [Streaming a CloudXR application to Apple Vision Pro with foveation](https://developer.apple.com/documentation/FoveatedStreaming/streaming-a-cloudxr-application-to-apple-vision-pro-with-foveation)
- [Creating a foveated streaming client on visionOS](https://developer.apple.com/documentation/FoveatedStreaming/creating-a-foveated-streaming-client-on-visionos)
- [Foveated Streaming](https://developer.apple.com/documentation/FoveatedStreaming)
- [StreamingSession: Streaming immersive content from a CloudXR™ application to visionOS and iOS](https://github.com/apple/StreamingSession)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/286/4/fa302edd-f95a-49f4-b51c-3899d49c6dec/downloads/wwdc2026-286_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/286/4/fa302edd-f95a-49f4-b51c-3899d49c6dec/downloads/wwdc2026-286_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/286/
