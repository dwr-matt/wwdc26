# Modernize your AppKit app

# WWDC 2026: Modernize Your AppKit App

## Overview
This session focuses on aligning AppKit applications with the modern macOS 27 experience. The presenter, an engineer from the Mac UI Frameworks team, explains how to replace legacy interaction patterns with modern, system-integrated APIs. The session is categorized into three pillars: providing precision input methods (moving away from manual tracking loops), ensuring seamless app continuity (graceful termination and state restoration), and adopting modern visual aesthetics (Liquid Glass updates and concentric corner configurations).

---

## Key Concepts, APIs, and Frameworks

### 1. Modern Input Methods
The session emphasizes replacing manual `mouseDown` overrides and tracking loops with event-handling frameworks that support cross-platform compatibility (SwiftUI/UIKit).
*   **Gesture Recognizers:** The preferred, modern way to handle interactions.
*   **Control Events:** Now available in AppKit, allowing developers to hook into `NSControl` events (similar to UIKit) without subclassing.
*   **NSTextSelectionManager:** A new macOS 27 API to provide system-native, bidirectional text selection and drag-and-drop to custom views.
*   **Expanded Interface Session:** An API to manage the lifecycle of custom UI triggered by status bar items, ensuring proper keyboard focus and window management.

### 2. State Restoration
To ensure a "seamless quit and restore" experience, the session advocates for:
*   **NSWindowRestoration:** The primary protocol for encoding and decoding UI state.
*   **Autosave Names:** Used for main windows and preference panes to preserve frame and space positioning.
*   **InvalidateRestorableState:** A critical method to call whenever a UI change (e.g., selection) occurs to signal that the state needs re-saving.

### 3. Visual Modernization
*   **Liquid Glass:** Updates for macOS 27 include improved scroll edge effects, sidebar emphasis, and a new "bouncing" interaction effect for Glass-based controls.
*   **Corner Configuration API:** Enables "Concentricity," where a view’s corner radius automatically adjusts to match the curve of its container.

---

## Code Patterns & Techniques

*   **Handling Input:**
    *   **Context Menus:** Use the `defaultMenu` property on `NSView` for global menus, or `menu(for:)` for event-dynamic menus.
    *   **Avoiding Overlap:** If a view blocks mouse events, override `hitTest(_:)` and return `nil` to allow events to pass through to content behind it.
*   **State Restoration Flow:**
    1.  Set `isRestorable = true` on the window.
    2.  Implement `encodeRestorableState(with:)` in your `NSResponder` subclasses, calling `super`.
    3.  Implement `restoreWindow(withIdentifier:...)` in a designated restoration class.
    4.  Call the provided `completionHandler` within the restoration method, even if an error occurs.
*   **Concentric Corners:**
    *   Override `cornerConfiguration` in an `NSView` subclass.
    *   Return an `NSViewCornerConfiguration` using `.containerConcentric` as the radius type to ensure it follows the parent's geometry.

---

## Practical Takeaways for Developers

1.  **Stop Overriding `mouseDown`:** Audit your codebase for manual tracking loops. Use built-in delegate methods for tables (`NSTableViewDelegate`), standard gesture recognizers, or control actions instead.
2.  **Optimize for Keyboard:** Ensure all custom UI elements are navigable via Tab/Shift-Tab. If you have custom windows (like those from a menu bar item), ensure they participate in the `Expanded Interface Session` to keep focus handling predictable.
3.  **Refine Quit Behavior:** Set `preventsApplicationTerminationWhenModal` to `false` for sheets or dialogs that do not require explicit user intervention, allowing the system to handle background updates or reboots gracefully.
4.  **Adopt the New Look:** Check your view hierarchies for instances where you manually rounded corners. Replace these with the `Corner Configuration API` to get automatic, system-aligned concentricity.
5.  **Don't Serialize Data:** When using state restoration, save only the UI state (e.g., selection IDs), not the entire application data model. The goal is to reconstruct the *view* state, not re-fetch the database.

<!-- resources -->

---

## Resources

- [Use SwiftUI with AppKit](https://developer.apple.com/videos/play/wwdc2022/10075/)
- [Restoring your app’s state with AppKit](https://developer.apple.com/documentation/AppKit/restoring-your-app-s-state-with-appkit)
- [Gestures](https://developer.apple.com/documentation/AppKit/gestures)
- [TN3212: Adopting gesture recognizers for Sidecar touch support](https://developer.apple.com/documentation/Technotes/tn3212-adopting-gesture-recognizers-for-sidecar-touch-support)
- [NSControl.Events](https://developer.apple.com/documentation/AppKit/NSControl/Events)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/289/5/6a2a7cfa-56a1-4cbb-ae54-1f229e1708ae/downloads/wwdc2026-289_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/289/5/6a2a7cfa-56a1-4cbb-ae54-1f229e1708ae/downloads/wwdc2026-289_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/289/
