# Use SwiftUI with AppKit and UIKit

# WWDC 2026 Summary: Use SwiftUI with AppKit and UIKit

## Overview
This session focuses on the incremental adoption of SwiftUI within existing AppKit and UIKit applications. The core message is that modern Apple development is not an "all-or-nothing" proposition; instead, SwiftUI is designed to coexist with legacy frameworks, allowing developers to modernize parts of their UI without rewriting their entire codebase. The presenter demonstrates how to synchronize data models using the `Observable` macro, embed SwiftUI views into traditional view hierarchies, integrate custom gesture recognizers, and add SwiftUI scenes to existing delegate-based app architectures.

---

## Key Concepts and APIs

### Data Synchronization
*   **`Observable` Macro:** The recommended way to keep data models and UI in sync. By using `@Observable` in your data models, `NSView` and `UIView` subclasses can automatically track property changes and trigger redraws (e.g., via `drawRect` or layout updates) without manual `setNeedsDisplay` calls. 
*   **Back-deployment:** Use `NSObservationTrackingEnabled` and `UIObservationTrackingEnabled` in your `Info.plist` to bring this functionality to macOS 15 and iOS 18 projects.

### Integration APIs
*   **`NSHostingView` / `UIHostingController`:** Standard wrappers used to embed SwiftUI views within an existing AppKit or UIKit hierarchy.
*   **`NSGestureRecognizerRepresentable`:** A protocol that allows developers to wrap existing `NSGestureRecognizer` subclasses (like custom Force Click logic) for use directly within a SwiftUI view hierarchy via the `.gesture()` modifier.
*   **`NSHostingMenu`:** A bridge class that allows you to create menus using SwiftUI `View`s and insert them directly into an existing AppKit `NSMenu` structure.
*   **`NSHostingSceneRepresentation`:** An API for adding SwiftUI scenes (like Menu Bar Extras or Settings windows) to an existing AppKit `AppDelegate` life cycle.

---

## Demonstrated Techniques

1.  **Incremental Modernization:** The session emphasizes using the `Observable` macro as a first step. This stabilizes the data layer, making it significantly easier to swap out individual UI components for SwiftUI versions later.
2.  **Immediate Mode Drawing:** Developers are encouraged to use the `Canvas` view in SwiftUI for custom drawing. It functions similarly to `drawRect` but provides a fresh graphics context for every redraw, and supports the `WithCGContext` API for reusing legacy Core Graphics code.
3.  **Cross-Platform UI:** The speaker highlights that modern system controls (like `NSSlider` and `NSSwitch`) are increasingly powered by SwiftUI under the hood. "Liquid Glass" and other system UI components use SwiftUI to share implementation logic across macOS and iPadOS.
4.  **Scene Management:** By using `NSHostingSceneRepresentation` inside `applicationDidFinishLaunching`, developers can selectively enable/disable SwiftUI-based features (like a Menu Bar extra) via user preferences, providing a modern interface within an aging app shell.

---

## Practical Takeaways for Developers

*   **Don't Rewrite, Migrate:** You do not need to convert your entire app. Start by adopting the `Observable` macro to simplify your current data-to-view update logic.
*   **Targeted SwiftUI Adoption:** Identify small, independent components (like a custom color picker or a new toolbar item) and implement them in SwiftUI. Use `NSHostingView` to place them inside your current screens.
*   **Bridge Your Gestures:** If you have highly specialized gesture logic (like complex hardware-specific touch or force-click interactions), do not rewrite them; use `NSGestureRecognizerRepresentable` to bring them into your new SwiftUI views.
*   **Menu and Scene Integration:** You can easily add modern SwiftUI-based menus or helper windows to an old codebase using `NSHostingMenu` and `NSHostingSceneRepresentation` without touching the legacy navigation logic.
*   **Documentation References:** For specific deep dives, the session points to:
    *   *What's New in UIKit* (WWDC 2025) for UIKit-specific observation.
    *   *Compose Advanced Graphics Effects with SwiftUI* (WWDC 2026) for Metal shader integration.
    *   *Add Rich Graphics to your SwiftUI app* (WWDC 2021) for `Canvas` usage.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/272/5/e1e4aa9a-cbe2-4f83-9cea-3dcaae19afd6/downloads/wwdc2026-272_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/272/5/e1e4aa9a-cbe2-4f83-9cea-3dcaae19afd6/downloads/wwdc2026-272_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/272/
