# Learn CSS Grid Lanes

# WWDC 2026: Learn CSS Grid Lanes

## Overview
This session introduces **CSS Grid Lanes**, a new native CSS layout mode designed to handle "masonry" or "waterfall" style layouts directly in the browser. Traditionally, these layouts required complex JavaScript libraries or hacky workarounds (like floats). Grid Lanes allows developers to create staggered, tightly packed layouts where items of varying heights automatically find their place in the shortest available column, preserving natural aspect ratios without distortion.

## Key Concepts
*   **Layout Philosophy:** Grid Lanes sits between Flexbox and CSS Grid. While Flexbox focuses on a single axis and Grid focuses on a fixed, two-dimensional coordinate system, Grid Lanes structures content along one axis while leaving the other "free" for items to settle into place.
*   **Flow Logic:** Items are placed one by one, automatically landing in the column that leaves them closest to the top of the container.
*   **Accessibility & Order:** The session emphasizes that visual flow can impact screen reader/tab order. To manage this, the browser uses **Flow Tolerance**, a mechanism that dictates how strictly the browser follows the "shortest column" rule.
*   **Compatibility:** Grid Lanes is available in Safari 26.4 and is currently available behind a flag in other browsers.

## Code Patterns & Techniques
### Basic Implementation
To activate Grid Lanes, use the `display` property:
```css
.container {
  display: grid-lanes;
  grid-template-columns: repeat(3, 1fr); /* Creates 3 equal columns */
  gap: 10px;
}
```
*To flip to a brick-wall (horizontal) layout, simply swap `grid-template-columns` for `grid-template-rows`.*

### Advanced Control
*   **Responsive Columns:** You can use `auto-fill` and `minmax()` to let the browser determine the number of columns based on container width:
    ```css
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    ```
*   **Spanning Items:** Use standard Grid properties to make specific items stand out:
    ```css
    .hero-item {
      grid-column: span 2;
    }
    ```
*   **Subgrid Integration:** You can nest layout modes. By adding `display: grid-lanes` and `grid-template-columns: subgrid` to a child element, its contents can participate in the parent's lane layout, effectively treating the card’s internal structure as part of the main grid.
*   **Flow Tolerance:** Adjusts the "strictness" of placement to balance visual appearance and logical ordering:
    ```css
    .container {
      flow-tolerance: 1em; /* Default value */
    }
    ```

## Practical Takeaways for Developers
1.  **Ditch the JavaScript:** Stop using heavy JS masonry libraries for image-heavy layouts; use native CSS to improve performance and reduce bundle size.
2.  **Tooling:** Use the Web Inspector (which has full Grid Lanes support) to debug. The inspector provides visual overlays that project column lines, row gaps, and item order numbers directly onto the canvas.
3.  **Think "Content-First":** Grid Lanes is adaptive. Unlike standard Grid, which forces items into fixed cells, Grid Lanes respects the natural size of your content. Use it for photo galleries, content feeds, or magazine-style layouts.
4.  **Experiment:** Refer to the **Grid Lanes Field Guide** provided by the WebKit team for interactive demos and to test how different content shapes affect the layout behavior.

<!-- resources -->

---

## Resources

- [WebKit.org - CSS Grid Lanes Field Guide](https://gridlanes.webkit.org)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/314/4/72928edd-5728-4010-b8f0-27f1a7bdec8c/downloads/wwdc2026-314_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/314/4/72928edd-5728-4010-b8f0-27f1a7bdec8c/downloads/wwdc2026-314_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/314/
