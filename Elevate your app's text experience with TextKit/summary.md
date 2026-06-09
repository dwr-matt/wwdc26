# Elevate your app's text experience with TextKit

# WWDC 2026: Elevate Your App’s Text Experience with TextKit

This session introduces developers to the latest advancements in Apple’s **TextKit** engine, focusing on bridging the gap between the convenience of high-level framework `TextView`s (like `UITextView` and `NSTextView`) and the granular control required for complex, custom text-editing experiences. The session demonstrates how to leverage new APIs to build specialized text features—such as line numbers, collapsible sections, and stateful text attachments—without sacrificing the performance or accessibility benefits of the platform's default text controls.

---

### Key Concepts & Architecture
TextKit utilizes a four-layer architecture shared across SwiftUI, UIKit, and AppKit:
1.  **Text Storage:** Encapsulates raw data (e.g., `NSTextContentStorage` or custom `NSTextContentManager` subclasses).
2.  **Layout:** Manages metrics and breaks text into immutable `NSTextLayoutFragment` objects.
3.  **Viewport:** Tracks visible text chunks using `NSTextViewportLayoutController` to ensure performance by rendering only what is on screen.
4.  **View:** The visual representation (`UIView`, `NSView`, or `CALayer`) where text is drawn.

### New APIs and Protocols
The 2027 release introduces APIs designed to help developers track and manage rendering surfaces:
*   **`NSTextViewportRenderingSurface`:** A protocol for visual elements inside the viewport that allows for custom drawing.
*   **`NSTextViewportRenderingSurfaceKey`:** A protocol (conformed to by `NSTextLayoutFragment`) that allows developers to cache and identify rendering surfaces across layout cycles, typically using map tables.
*   **`NSTextViewportLayoutControllerDelegate`:** Now supported by `UITextView` and `NSTextView`, this allows developers to hook into the layout process via `willLayout`, `configureRenderingSurface`, and `didLayout`.

---

### Demonstrated Code Patterns & Techniques
The session highlights three primary ways to extend standard framework text views:

#### 1. Adding Line Numbers
By overriding `UITextView` delegate methods, developers can:
*   Use `willLayout` to clear state and calculate the starting line number.
*   Use `configureRenderingSurface` to capture the `layoutFragmentFrame` for each paragraph.
*   Use `didLayout` to convert coordinates to the container's space and pass the information to a companion view for drawing line numbers.

#### 2. Collapsible Sections
By conforming to `NSTextContentStorageDelegate` and implementing `textContentManager(shouldEnumerate:)`, developers can conditionally skip layout for specific paragraphs. By maintaining a set of IDs for "collapsed" states, the app can selectively hide portions of text based on user interaction (e.g., toggling a disclosure button).

#### 3. Stateful Text Attachment Reuse
To prevent animations (like GIFs or custom views) from restarting during every edit, developers can use the new `register(for:type:)` API on `UITextView`:
*   **`onEditingInlineParagraphs`**: Prevents the `NSTextAttachmentViewProvider` from being destroyed when a user types in the same paragraph.
*   **`onScrollingOutOfViewport`**: Caches the surface when it exits the view and restores it upon return.

---

### Practical Takeaways for Developers
*   **Start with the Frameworks:** Use `UITextView` (UIKit), `NSTextView` (AppKit), or `TextEditor` (SwiftUI) by default. Use `ViewRepresentable` to bridge these into SwiftUI apps.
*   **Subclassing is Powerful:** You no longer need to build a custom engine from scratch to get high-level customization; by overriding the viewport layout delegate methods on standard `TextView` classes, you can achieve professional-grade results.
*   **Performance First:** Always utilize the `NSTextViewportLayoutController` process. Never perform heavy calculations inside the layout path; use caching and the new rendering surface keys to maintain 60/120 FPS performance.
*   **Syncing Views:** If you need multiple presentations of the same document, simply connect multiple `NSTextLayoutManagers` to the same `NSTextContentStorage` to ensure bidirectional synchronization.

<!-- resources -->

---

## Resources

- [Enriching your text in text views](https://developer.apple.com/documentation/UIKit/enriching-your-text-in-text-views)
- [TextKit](https://developer.apple.com/documentation/AppKit/textkit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/370/5/f61dbe38-7302-451a-b3ab-9851d5746315/downloads/wwdc2026-370_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/370/5/f61dbe38-7302-451a-b3ab-9851d5746315/downloads/wwdc2026-370_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/370/
