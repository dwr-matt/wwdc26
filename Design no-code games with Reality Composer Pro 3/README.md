# Design no-code games with Reality Composer Pro 3

# WWDC 2026: Design no-code games with Reality Composer Pro 3

## Overview
This session introduces **Reality Composer Pro 3** as a powerful tool for rapid game prototyping and development, specifically targeting the Apple Vision Pro. The presenter, Saschke Anselt, demonstrates how to build a fully interactive 3D game—a "squirrel and nut" scene—entirely through a visual, node-based scripting system called **Script Graph**. The session emphasizes an iterative "design-first" workflow that allows creators to move from conceptual ideas to functional on-device prototypes without writing traditional code, while also offering pathways to integrate SwiftUI and custom Swift logic for more complex requirements.

## Key Concepts, APIs, and Frameworks
*   **Script Graph:** A node-based visual scripting environment for defining game logic through event-driven flows.
*   **RealityKit Components:** The building blocks for game entities, including `InputTargetComponent` (for interactivity), `CollisionComponent` (for physics boundaries), `HoverEffectComponent` (for UI feedback), and `PhysicsBodyComponent` (for dynamic motion).
*   **Live Preview:** A workflow feature enabling developers to test interactions directly on the Vision Pro while making adjustments on the Mac.
*   **Subgraphs & Prototypes:** Tools for organizing logic. **Prototype Subgraphs** allow developers to package reusable logic nodes that appear in the project’s "Add Node" menu.
*   **Custom Node Libraries:** Enables the creation of user-defined events (e.g., `NutIsDragged`) to facilitate communication between separate entities within a scene.
*   **SwiftUI Integration:** The ability to render 2D interfaces, such as speech bubbles, as 3D attachments within the spatial environment.

## Code Patterns and Techniques
*   **Event-Driven Logic:** Triggering actions based on gestures (e.g., `On Drag`) or state changes.
*   **Data Manipulation with Set Nodes:** Using nodes like `Set Transform` or `Set Physics Body Component` to dynamically modify entity properties during runtime.
*   **Input Variables & Overrides:** Creating public variables (e.g., `dragSpeed`) that allow designers to tune gameplay values in real-time. The "Override" pattern allows multiple instances of the same object to hold unique variable values.
*   **Physics-Driven Interaction:** Rather than direct position mapping, the presenter demonstrates using `Add Force` nodes combined with `Drag Delta` (difference in movement over time) to create a more realistic, "tossable" feel for objects.
*   **Scene-to-Swift Communication:** Using `Send Scene Event` in the Script Graph to trigger logic in Xcode, bridging the gap between no-code visuals and professional Swift code for advanced tasks (like triggering SwiftUI UI elements).

## Practical Takeaways for Developers
1.  **Iterate on Device:** Use the "Preview on Device" mode early and often. Adjusting parameters (like `dragSpeed`) while wearing the Vision Pro provides immediate haptic and visual feedback that cannot be replicated in a 2D viewport.
2.  **Organize with Prototypes:** Don’t let your Script Graph become a "spaghetti" of nodes. Group repetitive logic into subgraphs and promote them to prototypes to streamline project maintenance and reuse.
3.  **Use Physics for "Juice":** Adding physics components significantly improves the "feel" of interactions. Instead of simple translation, calculate forces to make objects feel physical and reactive.
4.  **Hybrid Development:** You don’t have to choose between visual scripting and coding. Use Script Graph for the primary interactivity and logic, but pivot to Xcode for platform-specific UI (SwiftUI) or complex backend requirements by utilizing `Send Scene Event` nodes.
5.  **Utilize Coding Intelligence:** When extending logic into Xcode, use modern AI coding assistants to handle the boilerplate of subscribing to scene events or bridging logic between RealityKit and SwiftUI.

*Developers can download the "Squirrel" sample project from the Apple Developer website to inspect these techniques in practice.*

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/252/6/572c2388-69f6-4e57-9eba-c71b65f5f6ed/downloads/wwdc2026-252_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/252/6/572c2388-69f6-4e57-9eba-c71b65f5f6ed/downloads/wwdc2026-252_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/252/
