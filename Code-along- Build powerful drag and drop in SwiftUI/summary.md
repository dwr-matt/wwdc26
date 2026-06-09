# Code-along: Build powerful drag and drop in SwiftUI

## WWDC 2026: Build Powerful Drag and Drop in SwiftUI

### Session Overview
This session introduces significant enhancements to the SwiftUI drag and drop framework, focusing on providing developers with finer control over how content is moved, rearranged, and transferred. Using a Solitaire card game as a practical example, the presenter demonstrates how to implement intuitive reordering, support multi-item selection, and manage complex data transfer logic between different sections of an application. The session emphasizes composing these new modifiers to build a robust, fluid user interaction model that feels native to Apple platforms.

### Key Concepts, APIs, and Frameworks
*   **Reorderable APIs:** 
    *   `.reorderable`: A modifier applied to individual items (e.g., within a `ForEach`) to enable lifting and repositioning.
    *   `.reorderContainer`: A modifier applied to the parent view to manage the scope and logic of reordering, providing a closure to handle the `difference` in data once a drop occurs.
*   **Drag Container APIs:**
    *   `.dragContainer`: Enables users to drag multiple items simultaneously. It works alongside `reorderContainer` to define what happens when a user initiates a drag gesture on a selected set of items.
*   **Drag/Drop Configuration:**
    *   `.dragConfiguration`: Allows developers to specify the intent of a drag (e.g., `move` vs. `copy`) to prevent unintended data duplication.
    *   `.dropConfiguration`: Gives the destination view the "final say" on how data is received, allowing for validation of move operations based on app-specific rules.
*   **Visual Enhancements:**
    *   `.dragPreviewsFormation` & `.dropPreviewsFormation`: Customize the visual presentation of dragged items (e.g., `pile`, `list`, or `stack`) for a polished, consistent user experience.

### Code Patterns and Techniques
*   **Scoped Reordering:** To prevent specific items from being moved (e.g., face-down cards in Solitaire), the developer splits the data source into two separate `ForEach` views—one that is `.reorderable` and one that is not.
*   **Multi-Item Selection:** The presenter implements a simple "tap-to-select" model. When a drag is initiated on a selected card, the `dragContainer` closure is used to dynamically identify and bundle all selected items into a single transfer session.
*   **Transfer Validation:** The `.dropConfiguration` closure acts as a gatekeeper. By inspecting the session data and the proposed drop location, the app can return a "forbidden" operation to reject invalid drops, causing the dragged items to return to their source.
*   **Compositional Modifiers:** The session demonstrates the power of stacking modifiers. You can start with basic `reorderable` functionality and layer `dragContainer` and `dropConfiguration` on top to build increasingly complex interactions without rewriting the underlying logic.

### Practical Takeaways for Developers
*   **Leverage Existing Knowledge:** The session builds upon the `Transferable` protocol (introduced in 2022). Familiarity with `Transferable` remains the foundation for defining what can be moved in your app.
*   **Think in Moves, Not Just Copies:** When designing custom drag-and-drop, explicitly define whether your app intends to copy or move data. Using the new configuration modifiers ensures the system behaves as expected, especially in apps that manage state-heavy collections like games or data managers.
*   **Prioritize Visual Feedback:** Don't neglect how items look while being moved. Using `PreviewsFormation` modifiers helps maintain the "mental model" of the data as it travels across the screen.
*   **Platform Availability:** These new APIs are available on all Apple platforms supporting drag and drop, with specific enhancements (like the `dragContainer` modifier) being available on iOS, iPadOS, and VisionOS 27, and all featured modifiers available on macOS 26 and newer.

<!-- resources -->

---

## Resources

- [Making a card game with drag, drop, and reordering in SwiftUI](https://developer.apple.com/documentation/SwiftUI/Making-a-card-game-with-drag-drop-and-reordering-in-swiftui)
- [Drag and drop](https://developer.apple.com/documentation/UIKit/drag-and-drop)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/271/5/07f08d32-e28e-476f-8ebe-a3600b2e917c/downloads/wwdc2026-271_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/271/5/07f08d32-e28e-476f-8ebe-a3600b2e917c/downloads/wwdc2026-271_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/271/
