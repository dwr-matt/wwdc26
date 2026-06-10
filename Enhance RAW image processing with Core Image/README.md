# Enhance RAW image processing with Core Image

# Session Summary: Enhance RAW image processing with Core Image

This WWDC 2026 session introduces **RAW 9**, the latest iteration of Apple’s RAW image processing pipeline. The session details how developers can leverage new Core ML-based algorithms to achieve superior demosaicing and noise reduction, alongside performance optimizations for both interactive editing and bulk batch exporting.

---

### Key Concepts, APIs, and Frameworks
*   **RAW 9 Pipeline:** A major update that utilizes a tiled Core ML model on the Apple Neural Engine to combine demosaicing and denoising for professional-grade image quality.
*   **CIRAWFilter API:** The core framework for loading, decoding, and applying adjustments to RAW image files. It automatically handles metadata, sensor data parsing, and high-quality rendering.
*   **CIImageProcessor API:** An advanced API for custom image processing kernels. It has been updated to support more granular control over memory and hardware utilization.
*   **Supported Camera Models:** A new class method that allows apps to dynamically query which camera models are compatible with specific RAW versions.

---

### Code Patterns and Techniques

#### 1. Enabling RAW 9
RAW 9 is not enabled by default. Developers must verify support and explicitly opt-in via the `CIRAWFilter` properties:
```swift
// Check for version 9 support
if rawFilter.supportedDecoderVersions.contains(9) {
    rawFilter.decoderVersion = 9
}
```

#### 2. Explicit Output Tile Sizes
To optimize memory usage, developers can now manually define tiling strategies for the `CIImageProcessor` by creating an array of tile regions:
*   Define a custom tiling strategy (e.g., 512x512 pixels).
*   Pass the tile array to the `apply` method.
*   This prevents memory spikes by ensuring the processor only operates on defined subsets of the image.

#### 3. Temporary Buffers for CIImageProcessor
To improve performance when converting between interleaved and planar data (required for Core ML), developers can now request scratch buffers:
*   Use `output.temporaryPixelBuffer(withIdentifier:)` within the process callback.
*   Core Image manages the lifecycle, recycling, and release of these buffers automatically, reducing overhead for multi-tile operations.

---

### Practical Takeaways for Developers

#### For Interactive Editing (Responsive UI)
*   **Use `scaleFactor`:** Reduce rendering work by setting the scale factor when displaying images at screen resolution rather than full sensor resolution.
*   **Cache Intermediates:** Set `cacheIntermediates = true` on your `CIContext` to ensure Core ML results are cached during slider adjustments (e.g., exposure/sharpness).
*   **Use Metal Views:** Render directly to `MTKView` to allow asynchronous frame processing.

#### For Bulk Exporting (High Performance)
*   **Disable Caching:** Set `cacheIntermediates = false` to save memory during batch processing.
*   **Increase Memory Limits:** Use `contextMemoryLimit` to allow Core Image more headroom (up to 1024 MB) for faster processing.
*   **Use Native Methods:** Prefer `heifRepresentation(of:...)` or `jpegRepresentation(of:...)` over manual `ImageIO` calls for better performance.

#### Important API Changes in RAW 9
*   **Deprecated/Removed Properties:** `colorNoiseReductionAmount`, `detailAmount`, and `moiréNoiseReductionAmount` are no longer needed as the RAW 9 Core ML model handles these automatically.
*   **Compatibility:** Always use `isSupported` checks on filter properties before attempting to adjust them to ensure compatibility across different RAW versions.
*   **Extensibility:** The list of supported camera models is updated via over-the-air OS updates, ensuring your app benefits from new camera support without needing code changes.

<!-- resources -->

---

## Resources

- [Extended Virtual Addressing Entitlement](https://developer.apple.com/documentation/BundleResources/Entitlements/com.apple.developer.kernel.extended-virtual-addressing)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/305/5/d8d5f3ce-0ff1-45a3-a630-436743477c62/downloads/wwdc2026-305_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/305/5/d8d5f3ce-0ff1-45a3-a630-436743477c62/downloads/wwdc2026-305_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/305/
