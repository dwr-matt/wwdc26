# Compose advanced graphics effects with SwiftUI

# WWDC 2026: Compose Advanced Graphics Effects with SwiftUI

This session presents a framework for building complex, high-fidelity UI components by treating SwiftUI development as a "creative pipeline." The presenter, Hauqian from the UI Frameworks team, demonstrates how to move beyond basic layouts by chaining standard APIs—specifically shaders, time-based timelines, and custom alignment guides—to create professional, Apple-grade interface effects like dynamic background animations and time-synced transcripts.

---

### Key Concepts & Frameworks
*   **The Creative Pipeline:** The philosophy that advanced UI is not about complexity, but about the construction of individual, modular "pipes" (modifiers and APIs) that pass data from one stage to the next.
*   **Metal Shaders in SwiftUI:** SwiftUI provides direct hooks into the GPU via three specific shader effect APIs. These allow developers to manipulate pixels in parallel without the overhead of manual drawing.
*   **Stateless Shaders:** Unlike typical SwiftUI animations, shaders are stateless and do not store memory of previous frames. Animation is achieved by passing time-based inputs into the shader.
*   **Alignment Guides:** A powerful layout tool that allows for precise, coordinate-independent positioning by overriding how views determine their "alignment points."

### APIs Discussed
*   **Shader Effect APIs:**
    *   `colorEffect`: Transforms individual pixel colors (useful for grayscale or tinting).
    *   `distortionEffect`: Samples positions from an original image (useful for geometric transformations).
    *   `layerEffect`: The most flexible; provides access to the entire layer, allowing for sampling of adjacent pixels (useful for blurs and advanced distortion).
*   **`TimelineView`:** The primary mechanism for driving time-based animations by providing a persistent stream of timestamps to shaders.
*   **`alignmentGuide`:** Used to move the anchor point of a view relative to its container without using hardcoded offsets.

---

### Demonstrated Techniques
*   **Domain Warping:** A technique using noise textures to create "organic" visual effects. By sampling a noise texture once to get an offset, and then sampling it again based on that initial offset, developers can create complex, fluid-like "blobs" or liquid animations.
*   **Time-Synced UI:** Connecting `TimelineView` data to both GPU shaders (for background visuals) and standard SwiftUI scroll views (for transcript highlights) to ensure the entire UI stays in sync with playback state.
*   **Semantic Positioning:** Instead of calculating frames or using `offset(x:y:)`, developers are encouraged to override `alignmentGuide` to "pin" views (like timestamp overlays) to specific edges. This allows the layout system to automatically handle view sizing and positioning changes.

---

### Practical Takeaways for Developers
1.  **Deconstruct your Design:** Start by breaking your UI down into its atomic parts (background, content, labels). Treat each part as a pipe that transforms data into visuals.
2.  **Shader Flexibility:** If you need to perform an effect that requires knowing the values of surrounding pixels (like a blur or complex ripple), always reach for the `layerEffect` API first.
3.  **Use `TimelineView` for Shaders:** Remember that because shaders have no internal state, you must pass a changing parameter (usually a `TimeInterval` from `TimelineView`) into the shader function to enable animation.
4.  **Avoid Hardcoded Offsets:** When building dynamic interfaces, leverage custom alignment guides. They are more robust, handle screen rotations and dynamic type sizing better, and provide a cleaner, more semantic codebase.
5.  **Experiment with Noise:** Domain warping is a high-impact, low-effort way to add a "premium" feel to static backgrounds. Download the sample project provided by Apple to experiment with different noise parameters.

<!-- resources -->

---

## Resources

- [Alignment](https://developer.apple.com/documentation/SwiftUI/Alignment)
- [Composing advanced graphics effects with SwiftUI](https://developer.apple.com/documentation/SwiftUI/Composing-advanced-graphics-effects-with-SwiftUI)
- [Shader](https://developer.apple.com/documentation/SwiftUI/Shader)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/322/4/db4c622a-2091-45ef-a024-df317a5b55a5/downloads/wwdc2026-322_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/322/4/db4c622a-2091-45ef-a024-df317a5b55a5/downloads/wwdc2026-322_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/322/
