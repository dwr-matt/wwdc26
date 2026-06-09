# Discover the Spatial Preview framework

# Session Summary: Discover the Spatial Preview Framework

## Overview
This WWDC 2026 session introduces the **Spatial Preview** framework, a powerful new toolset designed to bridge the gap between macOS desktop applications and the immersive environment of visionOS. By leveraging Mac Virtual Display, the framework allows developers to extend their content—ranging from 2D documents and images to complex 3D scenes—directly into the user's physical space on Vision Pro. The session highlights how developers can create fluid, real-time collaborative workflows where content is synchronized between the Mac and Vision Pro, allowing for live iteration, annotation, and spatial review of creative projects.

## Key Concepts, APIs, and Frameworks
*   **Spatial Preview Framework:** The core framework enabling communication between macOS apps and visionOS.
*   **Document Preview Sessions:** Designed for non-3D media, including spatial photos, immersive video frames, PDFs, and standard images.
*   **USD Preview Sessions:** Built for 3D content using the **Universal Scene Description (USD)** format. It allows for high-fidelity 3D interaction, camera switching, and material overrides.
*   **USD Kit:** The recommended Swift-based framework for managing and editing 3D stages. It serves as the backbone for the 3D content capabilities within Spatial Preview.
*   **Device Endpoints:** Represents the Vision Pro device receiving the content. It leverages current Mac Virtual Display sessions or allows manual selection via an integrated device picker UI.
*   **SharePlay Integration:** Built-in support for collaborative sessions, allowing multiple users to view and edit the same spatial content in real-time.

## Code Patterns and Techniques
*   **Endpoint Observers:** Developers start by creating a `connected spatial endpoint observer` to identify the active Vision Pro destination.
*   **Session Management:**
    *   **Document:** Use `DocumentPreviewSession`. The `UpdateContents` method allows developers to swap assets (e.g., gallery view) without relaunching the entire session, ensuring a seamless experience.
    *   **3D:** Use `USDPreviewSession`. Once started, the content appears in a volumetric view that can be promoted to full immersion by the user.
*   **Optimization:** Spatial Preview automatically handles mesh decimation and texture downsampling for performance. Developers can opt-out using the `unmodified` parameter, though this carries a risk of the content being too complex to render.
*   **Synchronization & Events:** 
    *   Uses standard **USD notices** (e.g., `objectsDidChange`) to track changes.
    *   Subscribes to playback and animation events via the session API.
    *   Annotation synchronization: To make objects interactable/annotatable, they must be children of a `document annotation group` and tagged with `spatial editable` metadata.
*   **UI Integration:** The session demonstrates using standard SwiftUI sheets to house the device picker and button actions to trigger session starts/updates.

## Practical Takeaways for Developers
*   **Low Barrier to Entry:** Setting up a basic preview requires only a few lines of code to create an endpoint and start a session. 
*   **Real-time Iteration:** Developers building creative tools (like CAD or design software) can achieve immediate feedback loops where Mac-based edits (e.g., moving furniture or changing materials) reflect instantly in the user's spatial view.
*   **Automatic Tooling:** VisionOS provides built-in tools (camera selection, wireframe mode, annotation tools) for free once a `USDPreviewSession` is active. You do not need to build these UI elements manually.
*   **Collaboration:** By utilizing SharePlay, you can turn a solo review tool into a powerful collaborative platform, enabling stakeholders to review 3D designs together, regardless of their location.
*   **Refinement:** For developers with existing USD workflows, utilize the provided bridging APIs to sync existing internal model data with USD Kit, ensuring a smooth transition to the spatial preview environment.

<!-- resources -->

---

## Resources

- [Reducing the rendering cost of RealityKit content on visionOS](https://developer.apple.com/documentation/visionOS/reducing-the-rendering-cost-of-RealityKit-content-on-visionOS)
- [Spatial Preview](https://developer.apple.com/documentation/SpatialPreview)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/282/5/958c34c9-f20e-4c6d-826a-eeed7ce7ba9e/downloads/wwdc2026-282_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/282/5/958c34c9-f20e-4c6d-826a-eeed7ce7ba9e/downloads/wwdc2026-282_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/282/
