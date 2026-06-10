# Explore immersive website environments in visionOS

# Summary: Explore Immersive Website Environments in visionOS

## Overview
This WWDC 2026 session introduces developers to the immersive web capabilities on visionOS. It demonstrates how to elevate standard websites by integrating 3D environments that allow users to transition from an inline webpage experience into a fully immersive, spatial environment. Using the `<model>` element and the new Immersive API, developers can create interactive, high-fidelity experiences—such as theater seat previews or escape room game marketing—that blend seamlessly with Safari’s windowed content.

---

## Key Concepts, APIs, and Frameworks

*   **HTML `<model>` Element**: The core primitive used to display 3D assets (`.usdz` files). It supports both inline presentation and immersive transitions.
*   **Immersive API**: A set of JavaScript APIs modeled after the familiar Fullscreen API. It includes:
    *   `requestImmersive()`: Triggers the transition of a model element into the user's physical space.
    *   `exitImmersive()`: Ends the immersive session.
    *   `onimmersivechange`: An event to detect when the environment enters or exits immersive mode.
*   **RealityKit Annotations**: Custom metadata embedded within USDZ files (typically via a Blender plugin) to define specific spatial behaviors, such as video docking, light spill, and shadow-casting surfaces.
*   **Video Docking**: The ability to project an HTML5 video onto a specific surface (e.g., a virtual TV screen) within the 3D immersive environment.
*   **Image Controls API**: Allows developers to add native controls to images, enabling users to view panoramas and spatial photos in full, wrapped-around immersion.

---

## Code Patterns and Techniques

*   **Customizing Entity Transforms**: By default, the `<model>` element fits content to its container. Developers can override this using an identity matrix or specific translation/rotation values to align the 3D scene with the user's "eye level" or specific viewpoints (e.g., mapping a seat selection JSON to a camera perspective).
*   **Responsive Immersive State**: Developers should listen to `onimmersivechange` to adjust UI layout dynamically. For example, show an "Exit" button when in immersive mode, as users may use the Digital Crown to exit unexpectedly.
*   **Lazy Loading**: By setting `display: none` on the model element, developers can defer the download and decoding of large 3D assets until the user explicitly triggers an immersive request, saving memory and bandwidth.
*   **Animation Control**: Developers can play model animations triggered by web events (e.g., waiting for a video to finish before triggering a door-opening animation in the 3D scene).
*   **Shadow Casting**: Using a low-poly mesh tagged with a "Scene Understanding" component allows the browser to cast the shadows of Safari’s window onto the 3D environment, increasing spatial presence.

---

## Practical Takeaways for Developers

1.  **Optimization is Critical**: Environment models are heavier than simple objects. To maintain performance:
    *   **Reduce vertex counts** by removing hidden geometry.
    *   **Merge entities** to reduce the total number of objects.
    *   **Bake lighting into textures** to use "unlit" materials, reducing real-time shading costs.
    *   **Compress assets** using the `usd_crush` command-line tool.
2.  **User Experience**: 
    *   Always provide a clear "Exit" mechanism within the UI, but respect that the Digital Crown is the primary system-level exit.
    *   Keep the primary focus of the 3D experience visible without requiring the user to move the Safari window.
3.  **Cross-Platform Parity**: The `<model>` element works on macOS and iOS, while the immersive capabilities scale to enhance the visionOS experience.
4.  **Resources**:
    *   Use **WebKit.org** demos to test experiences directly in the visionOS simulator or hardware.
    *   File feedback and bug reports at [bugs.webkit.org](https://bugs.webkit.org).
    *   Consult related sessions: *Get Started with the HTML model element* and *Design Immersive Environments for Vision OS Apps and the Spatial Web*.

<!-- resources -->

---

## Resources

- [Download - Immersive model add-on for Blender](https://developer.apple.com/download/files/web-env-blender-plugin.zip)
- [WebKit.org - Theater Ticket Sales immersive website environment demo for Apple Vision Pro](https://webkit.org/demos/model-demos/ticket-sales.html)
- [WebKit.org - Escape Game immersive website demo for Apple Vision Pro](https://webkit.org/demos/model-demos/escape-room.html)
- [GitHub: Spatial Backdrop explainer](https://github.com/WebKit/explainers/tree/main/spatial-backdrop)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/320/4/e1844891-477b-4612-ad8d-10e55bf395ba/downloads/wwdc2026-320_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/320/4/e1844891-477b-4612-ad8d-10e55bf395ba/downloads/wwdc2026-320_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/320/
