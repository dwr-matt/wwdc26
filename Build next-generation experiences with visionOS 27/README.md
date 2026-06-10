# Build next-generation experiences with visionOS 27

# WWDC 2026 Session Summary: Build next-generation experiences with visionOS 27

## Overview
This session provides a comprehensive look at **visionOS 27**, focusing on new tools, frameworks, and workflows for spatial computing. The update emphasizes developer flexibility, offering three primary paths for app development: updating existing iOS/iPadOS apps, building native spatial experiences from scratch, and extending macOS/PC applications into the spatial environment. With the introduction of the M5-powered Apple Vision Pro, the session highlights advanced rendering capabilities, AI-assisted development tools, and new ways to integrate physical accessories into the virtual workspace.

---

## Key Concepts, APIs, and Frameworks

### 1. Rendering and RealityKit
*   **RealityKit Enhancements:** New features include **physical space lighting** for blending virtual lights with the real world, **projective texture APIs** for effects like caustics, and **custom reverb meshes** for authentic spatial audio propagation.
*   **Gaussian Splatting:** Built-in support allows developers to capture and render real-world objects with high photorealistic fidelity.
*   **Cloth Simulation:** New real-time simulation capabilities for realistic fabric movement and draping.

### 2. Reality Composer Pro 3
A major overhaul focused on speeding up creative workflows:
*   **AI Assistant:** Generates 3D assets and textures based on natural language prompts directly in the editor.
*   **Animation Graph:** A new state machine system for managing transitions between animations at runtime.
*   **ScriptGraph:** A node-based visual scripting system that allows for building interactive logic without writing Swift code in Xcode.
*   **Navigation Meshes:** Tools for generating and editing navigation paths (jumps, ladders, obstacles) for characters.

### 3. Cross-Platform & Streaming
*   **Spatial Preview Framework (macOS):** Allows developers to preview 3D/spatial content from a Mac directly into an Apple Vision Pro via QuickLook and collaborative SharePlay sessions.
*   **Full Video Streaming (PC/Cloud):** Utilizes **NVIDIA CloudXR** to stream OpenXR content from remote devices. It features foveated transport, where the stream quality prioritizes the user's focus area to reduce latency and bandwidth usage.
*   **Game Engine Support:** Continued refinement for Unity, Unreal, and Godot, including plugins for spatial accessories and spatial audio.

### 4. Interactivity and Spatial Accessories
*   **Enhanced Object Tracking:** High-frame-rate tracking and improved CreateML training (including robustness for hand-held objects).
*   **Custom Spatial Accessories:** A new platform allowing developers to build custom physical peripherals. These use a combination of IR LEDs, IMUs, and Bluetooth to provide tactile, low-latency inputs (e.g., steering wheels, flight sticks).

### 5. Immersive Media
*   **Immersive Media Support (IMS):** Updated with camera presentation override commands and an **Immersive Preview Renderer** for real-time editorial feedback.
*   **Static Foveation:** A technique for encoding high-acuity immersive video that remains streamable without sacrificing visual density.

---

## Code Patterns and Techniques
*   **Deployment Targets:** Developers can bring legacy apps to visionOS simply by checking a box in App Store Connect or adding "visionOS" as a deployment target in Xcode.
*   **Composition Services:** Used for custom rendering engines to bypass native frameworks while maintaining deep system integration.
*   **Aspect Ratio Portals:** New API support in `AVPlayer` (UIKit) and `AVPlayer` (RealityKit) to allow immersive content to be viewed in a wide, non-full-immersion portal.
*   **Object Pose API:** New API for obtaining object positions in "metric space," enabling high-precision measurement applications without display corrections.

---

## Practical Takeaways for Developers
1.  **Iterate Faster with Reality Composer Pro:** Stop relying solely on Xcode; use the new **ScriptGraph** and **Assistant** to build logic and generate scenes visually.
2.  **Leverage Existing Hardware:** If you have a professional-grade PC application (like a flight simulator or CAD tool), use the **Full Video Streaming** framework to bring it to Vision Pro via CloudXR rather than porting the entire codebase.
3.  **Enhance User Interaction:** Consider building or integrating **Spatial Accessories** if your app requires physical interaction (e.g., flight controls or haptic tools). The system handles the latency and tracking; you only need to process the input.
4.  **Prioritize Fidelity in Media:** Use the new **Immersive Preview Renderer** and **Static Foveation samples** to ensure high-resolution media remains performant and visually crisp during streaming.
5.  **Expand Web Reach:** Ensure web assets are optimized for the new wide-aspect-ratio windows in Safari and consider implementing web environments to increase user immersion.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/287/4/979d9278-8250-46f9-ac82-79669ba7b479/downloads/wwdc2026-287_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/287/4/979d9278-8250-46f9-ac82-79669ba7b479/downloads/wwdc2026-287_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/287/
