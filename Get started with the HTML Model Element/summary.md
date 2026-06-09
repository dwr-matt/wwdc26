# Get started with the HTML Model Element

# WWDC 2026: Get Started with the HTML `<model>` Element

## Session Overview
This session introduces the native HTML `<model>` element, a new standard for displaying 3D content on the web. Previously exclusive to visionOS, the `<model>` element is now available across Safari on iOS, iPadOS, and macOS. It allows developers to embed 3D assets as easily as images or videos, removing the dependency on third-party JavaScript libraries like "Model Viewer." The session covers the entire lifecycle of 3D web content: asset preparation, implementation, interactivity, performance optimization, and cross-platform compatibility.

---

## Key Concepts, APIs, and Frameworks
*   **The `<model>` Element:** A native HTML tag that renders 3D content directly in the browser. It supports automatic stereoscopic rendering on Vision Pro and functions as an emerging W3C web standard.
*   **USDZ (Universal Scene Description):** The recommended file format. It encapsulates geometry, materials, textures, and animations into a single, optimized file.
*   **Polyfills:** Because the `<model>` element is a developing standard, a polyfill is available via the W3C for browsers that do not yet support native rendering.
*   **Ready Promise:** An asynchronous API used to detect when a model has finished loading, allowing developers to hide loading spinners or show error states.
*   **Stage Modes:** The `stage="orbit"` attribute enables built-in user interaction (rotation/tilt) without custom code.
*   **EntityTransform:** A property that allows developers to precisely manipulate a model’s orientation in 3D space via a `DOMMatrix`.
*   **AR Quick Look:** By adding `rel="ar"` to the model tag, developers can provide a seamless transition to Augmented Reality experiences on iOS and iPadOS.

---

## Code Patterns & Techniques

### 1. Basic Embedding and Fallback
The element supports standard image tags for graceful degradation in unsupported browsers:
```html
<model src="product.usdz">
  <img src="fallback.jpg" alt="3D product view">
</model>
```

### 2. Loading State Handling
Using the `ready` promise to manage UI states:
```javascript
const model = document.querySelector('model');
model.ready.then(() => {
    hideSpinner();
}).catch(err => {
    showFallback();
});
```

### 3. Custom Interactions
To disable standard orbit behavior and implement custom controls (like a side-view button):
```javascript
// Disable orbit
model.setAttribute('stage', 'none');

// Apply a rotation matrix
let matrix = new DOMMatrix();
matrix.rotateSelf(0, 135, 0); // Rotate 135 degrees on Y axis
model.entityTransform = matrix;
```

### 4. Animation Control
The `<model>` element automatically plays the first animation track. Playback can be modified via script:
```javascript
model.playbackRate = 5; // Fast forward
model.play();
```

---

## Practical Takeaways for Developers

*   **Asset Pipeline:** Use a "Capture-Convert-Create" approach. Leverage iPhone scanning, 3D software (Blender/Maya), or generative AI tools to create USDZ assets.
*   **Optimization:** Use the command-line tools `usdcrush` (to reduce file size without losing visual quality) and `usdrecord` (to generate automatic fallback thumbnail images from 3D models).
*   **Performance:** 3D files can be large; always optimize assets before production to ensure a smooth user experience.
*   **Design Considerations:** The `<model>` element renders in its own virtual space. You can set the `background-color` directly on the tag to match your page theme, but note that it will always render opaquely.
*   **Future-Proofing:** Since this is an open standard, developers are encouraged to join the **W3C Immersive Web Community Group** to provide feedback and shape the future of the specification.
*   **Compatibility:** While the element works across the Apple ecosystem, verify feature parity if relying on a polyfill, as some features (like stereoscopic rendering on Vision Pro) are inherently hardware-dependent and cannot be fully polyfilled.

<!-- resources -->

---

## Resources

- [WebKit.org - Theater Ticket Sales immersive website environment demo for Apple Vision Pro](https://webkit.org/demos/model-demos/ticket-sales.html)
- [The HTML model element in Apple Vision Pro](https://webkit.org/blog/17118/a-step-into-the-spatial-web-the-html-model-element-in-apple-vision-pro/)
- [GitHub: model element samples](https://immersive-web.github.io/model-element-samples/)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [AOUSD – Alliance for OpenUSD](https://aousd.org)
- [w3.org – Model element](https://immersive-web.github.io/model-element)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/215/4/b7d159c9-ee29-45d9-80f5-87b6a1c90565/downloads/wwdc2026-215_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/215/4/b7d159c9-ee29-45d9-80f5-87b6a1c90565/downloads/wwdc2026-215_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/215/
