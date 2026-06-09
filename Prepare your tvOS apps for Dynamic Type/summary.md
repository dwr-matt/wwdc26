# Prepare your tvOS apps for Dynamic Type

# WWDC 2026: Prepare your tvOS apps for Dynamic Type

## Overview
This session introduces system-wide Dynamic Type support for **tvOS 27**, allowing users to scale text across the entire platform. As an accessibility-first feature, it enables users to adjust text sizes from "Large" up to "Accessibility Extra Extra Extra Large." The session guides developers on how to adopt this feature to ensure their apps remain readable and functional, avoiding common pitfalls like text truncation or UI clipping.

## Key Concepts, APIs, and Frameworks
*   **Dynamic Type Support:** Parity with iOS, where UIKit and SwiftUI components (labels, buttons, tab bars) automatically adjust to user-selected text sizes.
*   **Accessibility Nutrition Labels:** Developers are encouraged to update their App Store metadata to indicate that their app supports larger text, helping accessibility-focused users find compatible apps.
*   **Frameworks:** Extensive use of `SwiftUI` for reactive layout changes and `UIKit` for traditional view-based interfaces.
*   **Environment Values:** The use of `dynamicTypeSize` in SwiftUI to detect when a user has enabled larger text preferences.

## Techniques and Code Patterns
The session emphasizes moving away from static, hard-coded UI in favor of flexible, fluid layouts:

### 1. Removing Hard-coded Constraints
*   **Fonts:** Avoid fixed font sizes. Use semantic text styles (e.g., `.caption`, `.body`) which automatically scale.
*   **Width/Height:** Remove fixed width constraints (e.g., `frame(width: 300)`) that cause truncation when text expands. Use flexible constraints like `maxWidth: .infinity`.
*   **UIKit:** For UIKit users, ensure `adjustsFontForContentSizeCategory` is set to `true` to trigger automatic updates.

### 2. Conditional Layout Strategies
When standard scaling isn't enough, developers should pivot the UI:
*   **Grid/Collection Adjustment:** When text is too large to fit in a multi-column layout, use the `dynamicTypeSize` environment value to reduce the column count (e.g., switching from 6 columns to 4).
*   **Stack Reorientation:** Use the `AnyLayout` protocol in SwiftUI to switch between `HStack` and `VStack` dynamically based on the text size.
*   **UIKit Adaptation:** Use `UIStackView` and update the `axis` property within `traitCollectionDidChange` or by registering for `UITraitPreferredContentSizeCategory`.

## Practical Takeaways for Developers
*   **Audit your codebase:** Search for and eliminate any hard-coded font sizes, fixed heights, or rigid width constraints.
*   **Prioritize legibility:** If an element grows, ensure the container has enough padding and vertical space to accommodate the expanded content.
*   **Systematic Testing:** Test your interface specifically at the maximum accessibility text sizes. Ensure that UI elements do not overlap, clip, or truncate vital information.
*   **Declare Support:** Update your accessibility nutrition labels in the App Store; this builds trust and visibility with users who rely on these accessibility features.
*   **Default Behavior:** Lean on standard system components first, as they handle most of the scaling work automatically. Only apply custom layout logic when specific designs break under larger text sizes.

<!-- resources -->

---

## Resources

- [Applying custom fonts to text](https://developer.apple.com/documentation/SwiftUI/Applying-Custom-Fonts-to-Text)
- [Scaling fonts automatically](https://developer.apple.com/documentation/UIKit/scaling-fonts-automatically)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/221/5/ada10ebd-34f8-4f57-92b5-4b3cd6281267/downloads/wwdc2026-221_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/221/5/ada10ebd-34f8-4f57-92b5-4b3cd6281267/downloads/wwdc2026-221_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/221/
