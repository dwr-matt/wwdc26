# Supercharge your spatial workflows with Reality Composer Pro 3

# Session Summary: Supercharge your spatial workflows with Reality Composer Pro 3

## Overview
This session introduces the powerful new node-based visual tools in **Reality Composer Pro 3 (RCP 3)** designed to streamline the creation of high-fidelity spatial experiences. By utilizing a collection of integrated editors—including Animation Graph, Behavior Trees, Script Graph, and Compute Graph—developers can prototype and iterate on complex interactions, character behaviors, and visual effects directly within the editor without relying on extensive traditional coding cycles.

---

## Key Concepts, APIs, and Frameworks
The session centers on the expansion of RCP 3’s visual authoring capabilities within the **RealityKit** ecosystem:

*   **Animation Graph:** A state-machine-based visual editor for character movement, supporting motion warping, inverse kinematics, and blend spaces.
*   **Behavior Trees:** A hierarchical node-based system for defining autonomous, multi-step entity logic (Sequences, Selectors, and Parallel nodes).
*   **Script Graph:** An event-driven visual scripting system that handles inter-entity communication, user interactions (like gestures), and state management.
*   **Navigation Mesh:** A system for spatial pathfinding, allowing characters to navigate complex geometry while avoiding obstacles, with support for "off-mesh connections" (e.g., climbing ladders).
*   **Compute Graph:** A Metal-backed, GPU-driven particle system for real-time simulations, divided into Emitter, Initialized, Simulate, and Output phases.
*   **Shader Graph Enhancements:** Introduction of new advanced shaders, including **RealityKit PBR Surface 2** (with Subsurface Scattering and Sheen), **Hair Surface** (for realistic fur/hair), and **Portal** nodes.

---

## Code Patterns and Techniques Demonstrated
*   **State Machine Transitions:** Using Boolean parameters (e.g., `isWalking`) to drive transitions between animation clips, allowing for smooth blending between idle and movement states.
*   **Behavioral Sequencing:** Organizing complex tasks (rotating, moving, waiting) into a parent-child node hierarchy, with preconditions used to gate logic (e.g., "Wait at table until `readyToBrew` is true").
*   **Event-Driven Interaction:** Mapping gestures (e.g., `onTap`) to `Set Entity Parameter` nodes to trigger state changes within the behavior tree, demonstrating a decoupled architecture.
*   **Subgraphs:** Utilizing reusable logic blocks—similar to functions—to perform repetitive initialization tasks or scene-wide setups.
*   **Particle Lifecycle Management:** Using the four-phase Compute Graph approach to handle particle birth (emitter), attribute randomization (initialized), movement logic (simulate), and visual presentation/fading (output).

---

## Practical Takeaways for Developers
1.  **Iterative Workflow:** Leverage **Live Preview** with Apple Vision Pro to make real-time adjustments to your scene. Visual node editing eliminates the need for constant build-compile-deploy cycles.
2.  **Modular Logic:** Use Subgraphs and Behavior Trees to keep your scene logic clean and modular. Separating your "routine" logic from your "interaction" logic (via Script Graph) makes your projects easier to debug.
3.  **Performance-First Effects:** Use the GPU-driven **Compute Graph** for particle effects instead of CPU-bound solutions to ensure high frame rates, which are critical for maintaining immersion in spatial computing.
4.  **Spatial Awareness:** Implement **Navigation Meshes** early in your level design to ensure characters can interact with your environment naturally. Use custom parameters to provide dynamic input to these systems at runtime.
5.  **Material Fidelity:** Utilize the updated **Shader Graph** nodes (PBR 2, Hair, Portal) to quickly reach high visual quality without writing custom Metal shaders, drastically reducing the time spent on material authoring.

### Related Sessions to Explore:
*   *Iterate your spatial scenes faster with Reality Composer Pro 3*
*   *Design No-Code Games with Reality Composer Pro 3*
*   *Explore Advances in Reality Kit*
*   *Explore materials in Reality Composer Pro*

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/393/4/e68b947f-f7f3-49b5-b959-7a70fd9899c3/downloads/wwdc2026-393_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/393/4/e68b947f-f7f3-49b5-b959-7a70fd9899c3/downloads/wwdc2026-393_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/393/
