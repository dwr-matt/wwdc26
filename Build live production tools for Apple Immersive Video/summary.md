# Build live production tools for Apple Immersive Video

# Session Summary: Build Live Production Tools for Apple Immersive Video

## Overview
This session introduces the technical ecosystem required to build live production tools for **Apple Immersive Video**. Jared King (Apple Immersive Video Live Engineering) outlines how to scale traditional broadcast production workflows—which typically handle 2D video—to meet the high-fidelity demands of immersive media. The session explains how Apple leverages established professional standards like **SMPTE 2110** and **ProRes** to create an end-to-end, high-quality production pipeline that allows developers to build tools for real-time capture, switching, recording, and replay of immersive content.

---

## Key Concepts, APIs, and Frameworks

### The Immersive Pipeline Foundation
*   **Production Domain vs. Delivery Domain:** The session focuses on the production domain (cameras, switchers, audio consoles, routers).
*   **Scale Requirements:** Apple Immersive Video requires 32x the resolution and 2x the frame rate of traditional 2D broadcast to match human visual acuity.
*   **SMPTE 2110 Integration:** The industry-standard protocol for professional media-over-IP. It uses multicast RTP to transmit video, audio, and metadata separately.
    *   **2110-22:** Transmits compressed ProRes immersive video (left and right eyes contained within a single stream).
    *   **2110-30:** Transmits uncompressed PCM audio (High-order Ambisonics and Spatial Audio objects).
    *   **2110-41:** Transmits frame-accurate metadata (JSON objects) including lens calibration and creative events.

### Core Frameworks
*   **AV Foundation / Video Toolbox / Core Audio:** The backbone for handling media I/O and processing.
*   **Immersive Media Support (IMS) Framework:** Introduced in visionOS 26, this is a purpose-built framework for reading/writing Apple Immersive Video metadata (e.g., lens calibration, camera IDs).
*   **ProRes:** Chosen as the standard codec due to its high fidelity, balance of image quality/bandwidth, and optimization on Apple Silicon.

---

## Techniques and Code Patterns

### Lossless Recording and Playout
A major challenge in traditional broadcasting is generational loss due to re-encoding. Apple’s solution keeps the media in a **"file-friendly" ProRes payload** throughout the entire lifecycle:
1.  **Direct Copy:** Because the live stream is already in ProRes, recording to disk uses a direct copy into an `.mov` container, avoiding unnecessary decode/re-encode cycles.
2.  **Metadata Tagging:** When writing the MOV video track via `AVAssetWriter`, developers must set the `kVTProjectionKindAppleImmersiveVideo` constant in the `AVVideoCompressionPropertiesKey`. This adds the necessary **VEXU (Video Extended Usage)** metadata to identify the file as immersive.
3.  **Metadata Synchronization:** Stream JSON data must be deserialized and parsed into metadata objects using the **IMS framework** before being written into the MOV's metadata tracks. This ensures the metadata remains synchronized with the video and audio frames during later playback.

### Playout Workflow
The playout process is the exact inverse of recording:
*   Media is read directly from the tracks in the MOV file.
*   Data is retransmitted back into the SMPTE 2110 network stream, essentially tricking the production system into treating a file playback as a "live" camera source.

---

## Practical Takeaways for Developers
*   **Standardize with SMPTE 2110:** If you are building professional production tools, interoperability with existing broadcast facilities via SMPTE 2110 is mandatory.
*   **Leverage Apple Silicon:** Use the hardware-optimized ProRes processing capabilities of Apple Silicon to handle the high-resolution, high-frame-rate requirements of immersive video.
*   **Adopt IMS Early:** The **Immersive Media Support (IMS)** framework is the primary tool for managing the complex metadata required for spatial environments. Refer to the documentation to handle camera/lens properties.
*   **Avoid Re-compression:** Architecture your tools to keep content in its native ProRes/PCM format. By passing through the raw data from 2110 streams into file containers (and vice versa), you maintain the high fidelity required for spatial immersion.
*   **Study the Ecosystem:** For network implementation details, consult the SMPTE standards library and check out related WWDC sessions on Apple Immersive Video playback to understand the end-to-end user experience.

<!-- resources -->

---

## Resources

- [kVTCompressionPropertyKey_ProjectionKind](https://developer.apple.com/documentation/VideoToolbox/kVTCompressionPropertyKey_ProjectionKind)
- [CMVideoCodecType](https://developer.apple.com/documentation/CoreMedia/CMVideoCodecType)
- [Apple ProRes RAW White Paper](https://www.apple.com/final-cut-pro/docs/Apple_ProRes_RAW.pdf)
- [Apple ProRes White Paper](https://www.apple.com/final-cut-pro/docs/Apple_ProRes.pdf)
- [Immersive Media Support](https://developer.apple.com/documentation/ImmersiveMediaSupport)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/338/5/4549be24-44c7-4214-ab9b-f21f9ed04691/downloads/wwdc2026-338_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/338/5/4549be24-44c7-4214-ab9b-f21f9ed04691/downloads/wwdc2026-338_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/338/
