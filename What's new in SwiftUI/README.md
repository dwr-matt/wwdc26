# What's new in SwiftUI

# WWDC 2026: What's New in SwiftUI

This session introduces the latest advancements in SwiftUI for the 2027 platform releases. The presentation highlights a refreshed design language, significant performance optimizations, and powerful new APIs for document management, toolbar control, and data flow. Developers can expect apps to gain a more modern look automatically, while new developer tools in Xcode 27—specifically "Agent Skills"—are designed to streamline the adoption of these new features.

## Key Concepts and APIs

### 1. Refreshed Design & Liquid Glass
*   **Automatic Appearance:** Apps gain an updated "Liquid Glass" look by default, featuring responsive tinting and improved interactivity on macOS and iOS.
*   **Active/Inactive States:** The system now automatically dims UI elements (icons/text) in inactive windows on iPad and Mac to improve visual hierarchy. Developers can use the `AppearsActive` environment value to manually dim custom elements.
*   **Responsive Toolbar:** New APIs allow for fine-grained control of toolbar items during window resizing:
    *   **Visibility Priority:** Use `visibilityPriority(.high)` to keep critical buttons visible.
    *   **Overflow Menus:** Use `ToolbarOverflowMenu` to group non-essential actions.
    *   **Pinning:** `topBarPinTrailing` keeps specific buttons pinned regardless of available space.
    *   **Auto-hiding:** `toolbarMinimizeBehavior(.onScrollDown)` hides the navigation bar automatically to maximize screen real estate.

### 2. Enhanced Document API
The Document API has been modernized to provide better performance and flexibility:
*   **Document Creation Source:** A new `DocumentCreation` API allows apps to offer different starting templates (e.g., "Blank" vs. "Photo") by passing a context to the initializer.
*   **Protocol-Oriented Architecture:** The `WritableDocument` and `ReadableDocument` protocols now work with `DocumentWriter` and `DocumentReader` to handle disk operations.
*   **Performance:** Write operations are now non-isolated and asynchronous, and they support differential updates (writing only changed parts of a file).
*   **Sharing:** Developers can easily export documents in different formats (e.g., PNG) by extending the `WritableContentTypes` and implementing custom CoreGraphics logic in the `write` method.

### 3. Presentation and Interaction
*   **Reorderable Containers:** The new `Reorderable` modifier works with `List`, `LazyVGrid`, and other containers to enable drag-and-drop reordering with minimal code.
*   **Swipe Actions:** Expanded support allows swipe actions on any view, not just `List`.
*   **Dialogs & Alerts:** `confirmationDialog` and `alert` now support the same item-binding pattern used by `sheet`, simplifying state-driven UI presentation.

### 4. Data Flow and Performance
*   **AsyncImage Caching:** `AsyncImage` now supports standard HTTP caching by default. Developers can customize this behavior via `URLSession` configuration or by passing custom `URLRequest` objects.
*   **State Macro Optimization:** `@State` properties are now "lazy" and only initialized once, even if the parent view re-initializes. This resolves performance issues where previous releases would instantiate redundant objects.
*   **Type-Checking Improvements:** A new unified `ContentBuilder` significantly reduces compiler load time by flattening the builder hierarchy, resolving long-standing "expression too complex" errors in deeply nested views.

## Practical Takeaways for Developers

*   **Xcode 27 Migration:** Rebuild your project in Xcode 27 to immediately benefit from the UI refreshes and the `ContentBuilder` optimization.
*   **Adopt Agent Skills:** Utilize the "SwiftUI Specialist" and "What's New in SwiftUI" skills in the Xcode 27 coding assistant to identify areas where your code can be modernized.
*   **Embrace Resizability:** Test your apps with the new resize handles in Xcode Live Previews to ensure your UI adapts correctly for features like iPhone Mirroring and iPad multitasking.
*   **Cleanup State Initialization:** Check your `init` methods; if you previously assigned default values to `@State` properties, remove them to prevent "use before initialization" errors resulting from the new lazy `@State` macro behavior.
*   **Leverage Swift Collections:** For reordering logic, integrate the open-source `SwiftCollections` package to efficiently commit changes back to your data sources.

<!-- resources -->

---

## Resources

- [State()](https://developer.apple.com/documentation/SwiftUI/State())
- [ContentBuilder](https://developer.apple.com/documentation/SwiftUI/ContentBuilder)
- [Swift Collections on GitHub](https://github.com/apple/swift-collections)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/269/4/9215cf93-1308-4706-91e8-34d4e40939d1/downloads/wwdc2026-269_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/269/4/9215cf93-1308-4706-91e8-34d4e40939d1/downloads/wwdc2026-269_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/269/
