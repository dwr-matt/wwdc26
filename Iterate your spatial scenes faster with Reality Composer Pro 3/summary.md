# Iterate your spatial scenes faster with Reality Composer Pro 3

# WWDC 2026: Iterate your spatial scenes faster with Reality Composer Pro 3

## Session Overview
In this session, Apple introduces **Reality Composer Pro 3**, a major update designed to streamline spatial development for visionOS. The tool shifts toward a more standalone, iterative, and AI-assisted workflow, reducing the need for constant round-trips to Xcode. By integrating features like live device previewing, advanced light mapping, and generative AI assistance, Reality Composer Pro 3 empowers developers to build, test, and polish complex 3D scenes with significantly lower friction.

---

## Key Concepts and Features

### 1. Standalone Application
Reality Composer Pro 3 is no longer bundled exclusively within Xcode. It is now a standalone application available via the Apple Developer portal, allowing for a dedicated focus on 3D scene composition and asset management.

### 2. Prototypes and Instancing
The new **Prototypes** system allows developers to convert any entity into a reusable asset.
*   **Workflow:** Drag an entity from the hierarchy into the Project Browser to create a Prototype.
*   **Overrides:** Instances of these prototypes can be customized (e.g., changing colors or graph parameters) without affecting the source. 
*   **Management:** Overrides can be reset to source values or propagated back to the prototype, ensuring a flexible, non-destructive workflow.

### 3. Light Maps
To optimize performance while maintaining visual fidelity, the tool now features **Light Map** baking for static geometry.
*   **Features:** Supports baking indirect lighting, Ambient Occlusion, and "Beauty" passes.
*   **Workflow:** Use the Light Map Preview tab to visualize results before committing to a high-quality bake, significantly enhancing scene depth under tables or in shadowed areas without requiring real-time lighting calculations.

### 4. Reality Composer Pro Assistant
A new built-in AI assistant accessible via the Inspector panel. It leverages generative models to:
*   Create 3D objects and materials on demand based on text prompts.
*   Answer "how-to" questions directly within the context of the editor.

### 5. Live Preview
Developers can now stream their scene directly to a connected Apple Vision Pro. This allows for "on-device" authoring, where changes in the editor are reflected in real-time within the actual spatial environment, eliminating guesswork regarding lighting, scale, and immersion.

---

## Code Patterns and Techniques

*   **Entity-Component Architecture:** The foundation of the scene remains the entity-component model. Developers use the Inspector panel to add components like `Transform`, `Point Light`, or `Compute Simulation` to entities.
*   **Compute Graphs:** GPU-accelerated node-based graphs are used for visual effects (e.g., particle systems, fluids).
    *   **Technique:** Graphs are assigned to entities via the **Compute Simulation Component**.
    *   **Iteration:** The "Simulation" tab allows developers to keep a simulation running while tweaking parameters (like "Twist amount" or color) in the Inspector in real time.
*   **Hierarchy Management:** Entities can be nested, and developers can use the shortcut **'F'** to frame selected objects in the viewport.

---

## Practical Takeaways for Developers

*   **Reduce Deployment Friction:** Utilize the new **Simulation tab** and **Live Preview** to avoid the "deploy-test-debug" loop. Make changes to physics, materials, and lighting while the scene is actively running.
*   **Adopt Prototypes:** Instead of manual duplication, use Prototypes to manage repetitive assets. This ensures that global updates (like changing a material or component setting) can be managed from a single source.
*   **Optimize Performance:** Use **Light Maps** for all static elements. This is the most efficient way to achieve high-quality, complex lighting in visionOS without sacrificing frame rates.
*   **Leverage Generative Tools:** When prototyping or stuck on a specific visual element, use the **AI Assistant** to scaffold 3D models or materials to maintain momentum.
*   **Consult Documentation:** For advanced topics, the session suggests exploring the "Supercharge Your Spatial Workflows with Reality Composer Pro 3" session, which covers deeper dives into Compute Graphs.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/280/4/0f02d465-7874-4ac3-aac3-b1b792efecd3/downloads/wwdc2026-280_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/280/4/0f02d465-7874-4ac3-aac3-b1b792efecd3/downloads/wwdc2026-280_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/280/
