# Dive into lazy stacks and scrolling with SwiftUI

# Session Summary: Dive into Lazy Stacks and Scrolling with SwiftUI

This WWDC 2026 session provides a deep dive into the internal mechanics of `LazyVStack` and `LazyHStack` in SwiftUI. The presenter, Renz, explains how these components optimize performance by loading views only when they enter the visible viewport, how they interact with `ScrollView`, and how developers can avoid common pitfalls that hinder performance and visual stability.

---

### Key Concepts & Frameworks
*   **LazyStacks:** Optimized containers that defer the evaluation and rendering of subviews until they are needed for display.
*   **Viewport Estimation:** Because `LazyStack` doesn't load all content at once, it uses an estimation system to determine the size of off-screen content. This allows for scrolling through large datasets efficiently.
*   **Prefetching:** A background mechanism where SwiftUI evaluates and lays out views just before they enter the visible rect to ensure smooth, hitch-free scrolling.
*   **Layout Lifecycle:** How `LazyStack` handles the lifecycle of subviews, including `onAppear` behavior and memory management for views that scroll out of sight.

---

### Code Patterns & Techniques
*   **Data-Level Filtering:** Instead of filtering views using conditional logic (like `if` statements or `environment` checks) inside a `View` body, developers should filter at the **data level** (e.g., using predicates in SwiftData/CoreData). This allows the `LazyStack` to accurately track the count and index of subviews.
*   **Avoiding Absolute Measurements:** Developers are warned against using `onScrollGeometryChange` to read absolute content offsets, as these offsets are based on estimations and are inherently unstable.
*   **Programmatic Scrolling:** Use `ScrollPosition` bindings for reliable navigation to specific items. Performance is optimized when each `ForEach` item resolves to exactly one view, allowing the system to locate the ID without constructing the view.
*   **Custom Layouts:** If a view requires changing its size after it has appeared (e.g., setting a state variable based on `onGeometryChange`), the presenter suggests using a **Custom Layout** instead of relying on multiple layout passes.

---

### Practical Takeaways for Developers

#### 1. Performance and Memory
*   **Keep View Hierarchies Flat:** Avoid complex conditional logic within leaf views inside a `LazyStack`. If a `View` body returns a variable number of views, the `LazyStack` must keep the entire hierarchy in memory to maintain correct indexing, defeating the purpose of being "lazy."
*   **State Management:** Move critical state into external Model objects or parent views. Local `@State` variables are destroyed when a view is removed from the `LazyStack` due to scrolling.
*   **Prefetching Efficiency:** Configure subviews in their initializers rather than performing heavy logic in `onAppear`. If data is required, initiate loading in the initializer or a dedicated Observable Object to ensure the view is ready before the user sees it.

#### 2. Scrolling Stability
*   **Avoid "Layout Thrashing":** Do not trigger layout updates after a view has appeared. If an image or text size changes after appearance, it pushes subsequent views, causing the `LazyStack` to re-estimate sizes, which leads to scrolling jitters.
*   **Relative Positioning:** Use `onScrollTargetVisibilityChange` to track view visibility instead of relying on absolute coordinates. This is much more reliable when working with estimated scroll offsets.
*   **Pinning Headers:** Use the `pinnedViews` parameter in `LazyVStack` to keep section headers anchored while scrolling, which remains a highly performant pattern.

#### 3. Summary of "Don'ts"
*   **Don't** use `onAppear` for essential data initialization if you want to avoid flickering or redrawing during the scroll.
*   **Don't** filter your list using view-level logic; always filter your underlying data source.
*   **Don't** assume the `LazyStack` knows the exact size of all content; plan for the height/width to be an estimation that stabilizes as content is loaded.

<!-- resources -->

---

## Resources

- [Grouping data with lazy stack views](https://developer.apple.com/documentation/SwiftUI/Grouping-Data-with-Lazy-Stack-Views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/321/5/78830752-d07d-4d89-aeab-94405c084de9/downloads/wwdc2026-321_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/321/5/78830752-d07d-4d89-aeab-94405c084de9/downloads/wwdc2026-321_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/321/
