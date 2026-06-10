# Collaborate on structured 3D models in visionOS

# Session Summary: Collaborate on Structured 3D Models in visionOS

## Overview
This session explores advanced techniques for handling complex, multi-dimensional 3D assemblies within visionOS. The primary goal is to move beyond simple 2D screen interactions by utilizing the spatial capabilities of Apple Vision Pro to conduct collaborative design reviews. The session demonstrates how developers can organize 3D asset hierarchies, implement interactive clipping planes for internal inspection, and use statistical analysis (variance/weighting) to automatically "explode" or expand assemblies for better visualization.

---

## Key Concepts, APIs, and Frameworks

### 1. RealityKit Components
*   **Manipulation Component:** The core API for enabling natural interaction. By applying this to specific sub-entities in a hierarchy, developers can grant users the ability to rotate, move, and scale individual parts of an assembly.
*   **Clipping Component:** A powerful tool in visionOS 27 that allows users to look inside complex assets by defining an Axis-Aligned Bounding Box (AABB) in entity-local space.
*   **Input Target Component:** Essential for event processing; must be paired with collision components to make entities interactable.

### 2. Mathematical Foundations
*   **Vector Projections:** Used to constrain drag gestures. By projecting a drag delta onto the normal of a clipping plane, the system ensures that user interactions feel intuitive and physically grounded.
*   **Weighted Variance:** Used to determine the best axis for "Auto-Expansion." By calculating the volume-weighted variance of sub-assemblies across X, Y, and Z axes, the application programmatically determines the most informative direction in which to "explode" a model.

---

## Code Patterns & Techniques

### Asset Hierarchy Management
*   **Structure is Interaction:** Assets should not be flattened. A deep, nested hierarchy is required for code to isolate, animate, or highlight specific components (e.g., pulling a single piston out of an engine block).
*   **Dynamic Re-parenting:** Instead of changing the model structure, toggle interactivity by moving the `ManipulationComponent` up to the root (to move the whole assembly) or down to children (to manipulate individual parts).

### Implementing Clipping Controls
*   **State Machine:** Use a three-state machine to manage clipping:
    1.  **Off:** Model is rendered fully.
    2.  **On:** `ClippingComponent` is active; renderer discards geometry outside the defined bounds.
    3.  **Editing:** Clipping planes are made visible as interactive entities. 
*   **Coordinate Frame Synchronization:** The session emphasizes four coordinate systems: **World**, **Model**, **Clipping Control**, and **Clipping Plane**. When a user drags a clipping plane, the code must:
    1.  Capture the gesture in the **Clipping Plane** frame.
    2.  Transform the delta to the **Model** frame.
    3.  Constrain the movement to the plane's normal (using dot products).
    4.  Update the clipping bounds and reflect the visual change back to the user.

---

## Practical Takeaways for Developers

*   **Prioritize Asset Structure:** Spend time during the modeling phase to ensure logical grouping. If your code cannot identify a "piston" as a unique entity, you cannot build meaningful spatial interactions for it.
*   **Use Visual Affordances:** When implementing features like clipping planes, provide clear visual UI (planes/colors) so users know they are in an "editing" state and understand how they can manipulate the model.
*   **Volume-Based Logic:** If your application requires "exploding" an assembly, don't hardcode the direction. Use the physics properties of your model (volume/position) to calculate the best axis for expansion, ensuring the UI remains intuitive regardless of the model loaded.
*   **Critical "Gotchas":**
    *   **Collisions:** Always include a `CollisionComponent` when using interaction APIs, or events will not be registered.
    *   **Clipping Children:** If your clipping isn't working as expected on parent assemblies, ensure `shouldClipChildren` is set to `true` in the `ClippingComponent`.
*   **Further Learning:** The session heavily relies on linear algebra and statistics. Developers are encouraged to revisit basic vector math (dot products, projections) to master these spatial interaction patterns.

<!-- resources -->

---

## Resources

- [Manipulating models with RealityKit](https://developer.apple.com/documentation/RealityKit/manipulating-models-with-realitykit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/284/4/fa1d15b1-3f28-415a-907a-8ae1bb344494/downloads/wwdc2026-284_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/284/4/fa1d15b1-3f28-415a-907a-8ae1bb344494/downloads/wwdc2026-284_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/284/
