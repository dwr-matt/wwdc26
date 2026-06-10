# What’s new in WebKit for Safari 27

# WWDC 2026: What’s New in WebKit for Safari 27

## Overview
In this session, Jen Simmons highlights the WebKit team’s strategic shift in focus for Safari 27: prioritizing the **quality and reliability of existing web standards** over simply shipping a high volume of experimental features. After delivering over 1,100 improvements and fixes since the previous fall, the team is focused on improving developer productivity and browser interoperability. The session balances these "under-the-hood" technical debt resolutions with several high-impact new features, including advanced CSS layouts, improved media elements, and streamlined cross-platform web extension distribution.

---

## Key Concepts, APIs, and Frameworks

*   **CSS Grid Lanes:** A powerful new way to implement masonry-style layouts using native CSS without relying on JavaScript.
*   **Customizable Select:** A major overhaul of the `<select>` element, allowing developers to style dropdown menus using CSS and insert rich HTML content (images, icons, subtext) while maintaining accessibility.
*   **The `<model>` Element:** Expanded support for native 3D object rendering. Now available on iOS, iPadOS, and macOS, it supports standard media attributes and integrates with AR (via `rel="ar"`) and immersive environments in VisionOS.
*   **Safari Web Extension Packager:** A new tool that allows developers to package and distribute Safari Web Extensions via App Store Connect from any web browser or operating system, removing the requirement to use Xcode or a Mac.
*   **MapKit JS:** An interactive mapping tool for the web that works across all browsers and operating systems, emphasizing user privacy.

---

## Code Patterns & Techniques

### Customizable Select UI
Developers can unlock custom styling by applying `appearance: base-select` to the `<select>` element and its associated pseudo-elements:
*   **`::picker`:** Targets the dropdown menu container, allowing for layout adjustments via Flexbox or Grid.
*   **`::picker-icon` / `::check-mark`:** Provides granular control over individual UI components.
*   *Note:* This allows for richer HTML content inside `<option>` tags, such as adding images or descriptions next to text.

### CSS Random Function
The team clarified the scoping for the `random()` function. It now defaults to **global scope**, meaning that if you assign a random value to a class, all instances of that class will receive the same random value, ensuring visual consistency across a page.

### Compatibility Fixes
*   **Emoji Input:** WebKit now handles characters requiring more than 16 bits (like many emojis) by sending them as text rather than attempting to pass a truncated Unicode number via legacy `fromcharcode` methods, preventing character corruption on older sites.
*   **Responsive Images:** The `sizes` attribute now correctly supports modern CSS functions like `min()`, `max()`, and `clamp()`, closing a long-standing interoperability gap.

---

## Practical Takeaways for Developers

1.  **Test Early and Often:** Use **Safari Technology Preview** or the **Safari Beta** to test your projects. The WebKit team relies on bug reports (via [bugs.webkit.org](https://bugs.webkit.org)) to prioritize their quality work.
2.  **Focus on Interoperability:** The team is moving toward a standard-driven future. If you are building web extensions, focus on the W3C standard API to ensure your single codebase remains compatible across different browser engines.
3.  **Modernize Legacy Layouts:** With the introduction of CSS Grid Lanes and the rewrite of block/inline layout engines, developers should look for opportunities to replace complex JavaScript-heavy masonry layouts with pure, performant CSS.
4.  **Leverage Native 3D:** Start incorporating the `<model>` element for product previews. It is now effectively a "first-class citizen" alongside `<audio>` and `<video>`, with built-in hooks for AR and immersive VisionOS experiences.
5.  **Distribute Anywhere:** You no longer need to be on a Mac to build for Safari users. Utilize the **Safari Web Extension Packager** to reach users regardless of your local development environment.

**Further Resources:**
*   **Field Guide to Grid Lanes:** [gridlanes.webkit.org](https://gridlanes.webkit.org)
*   **Official Documentation:** [developer.apple.com](https://developer.apple.com)
*   **WebKit Portal:** [webkit.org](https://webkit.org)