# Communicate your brand identity on iOS

# WWDC 2026: Communicate your brand identity on iOS

## Overview
This session explores the balance between establishing a distinct brand identity and maintaining the native, familiar experience that iOS users expect. Rather than defaulting to identical designs across platforms, developers are encouraged to contextually adapt their branding. The session introduces a structural approach—the "UI Layer" versus the "Content Layer"—to help developers identify where to lean into system standards and where to exercise creative expression, ensuring that branding serves the user experience rather than obstructing it.

## Key Concepts, APIs, and Frameworks
*   **The Two-Layer Design Model:**
    *   **UI Layer:** Comprised of global navigation and actions (tab bars, toolbars). This should prioritize familiar, native patterns to reduce the user’s cognitive load.
    *   **Content Layer:** The space beneath the navigation. This is the primary canvas for branding, including imagery, custom data visualizations, and unique content flows.
*   **Liquid Glass:** A new design language for iOS 26 that allows UI elements to float over content, dynamically picking up colors and maintaining a translucent, native feel.
*   **SwiftUI:** Highlighted as the primary framework for implementing modern, fluid, and accessible UI components natively.
*   **Dynamic Type:** A vital accessibility feature that ensures custom typography remains legible as users adjust their preferred font sizes.
*   **SF Symbols:** An extensive library of over 7,000 system-provided icons that scale, animate, and support accessibility out of the box, reducing the need for custom icon asset management.

## Techniques and Patterns
*   **Progressive Branding:** Use standard components (e.g., context menus, grid views) for utilitarian tasks. Reserve custom components for unique features that provide the most value to the specific app (e.g., the Moonlit lunar calendar).
*   **Edge-to-Edge Content:** Move away from solid-colored toolbars. Allow content to scroll behind transparent, Liquid Glass UI elements to maximize space and create a modern, immersive aesthetic.
*   **Contextual Color Usage:** Use color (tint/accent) to provide meaning, indicate interaction, or communicate status (e.g., unread badges) rather than filling broad backgrounds that clutter the interface.
*   **Motion and Transitions:** Use SwiftUI-driven transitions (e.g., Zoom transitions) to provide spatial context and delight. Ensure motion is performant; perceived performance (avoiding dropped frames) is a critical part of brand perception.
*   **Typographic Hierarchy:** Leverage system fonts (San Francisco variants like SF Rounded or New York) to maintain native harmony, or use custom fonts sparingly. If using custom fonts, you must implement support for **Dynamic Type** to prevent truncation and ensure accessibility.

## Practical Takeaways for Developers
1.  **Prioritize the "Native Feel":** Users should not have to "learn" how to use your app. If an interaction is standard, use a standard component.
2.  **Audit Your App:** Regularly review your codebase to replace overly-customized utilitarian elements (like navigation buttons) with native system equivalents.
3.  **Support System Preferences:** Your brand is only as good as the user’s comfort. Full Dark Mode support and compliance with Dynamic Type are essential for a professional, inclusive experience.
4.  **Practice Restraint:**
    *   **Icons:** Only build custom icons if they serve the brand significantly; otherwise, use SF Symbols. 
    *   **Logos:** Avoid over-branding. Hide or minimize logos on secondary screens so they don't consume valuable screen real estate.
5.  **Test for Accessibility:** Always verify that custom design choices—especially custom fonts and high-contrast color palettes—do not break accessibility requirements or fail to scale properly on different device sizes.
6.  **Refine, Don't Replicate:** When adapting your brand from web or other platforms, think about the *context* of a phone. Do not force an identical design; adapt the look and feel so it "belongs" to the iOS environment.

<!-- resources -->

---

## Resources

- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/251/4/52fb4c75-99ba-419f-90d6-bfef374ac966/downloads/wwdc2026-251_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/251/4/52fb4c75-99ba-419f-90d6-bfef374ac966/downloads/wwdc2026-251_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/251/
