# What's new in image understanding

# WWDC 2026: What's New in Image Understanding

## Overview
This session introduces powerful new capabilities for image understanding by bridging the gap between traditional computer vision and modern Large Language Models (LLMs). The presentation highlights the new **Tap to Segment API** in Vision, improved image support in the **Foundation Models Framework**, and a sophisticated **tool-calling architecture** that allows LLMs to leverage specialized Vision tasks (like barcode scanning and OCR) to solve complex user prompts. Additionally, the session demonstrates the expansion of Vision capabilities to **watchOS**, enabling developers to create more intelligent, context-aware experiences on Apple's smallest form factor.

---

## Key Concepts, APIs, and Frameworks

### 1. Vision Framework
*   **Tap to Segment API:** A new, interactive segmentation capability that allows users to select objects via points, bounding boxes, lassos, or scribbles. 
*   **Asset Management:** Introduces `Download Assets API` and `Asset Status` to handle the required on-device models for segmentation.
*   **Saliency Analysis:** Used for identifying "subjects of interest" in images, crucial for dynamic cropping on limited-space displays like the Apple Watch.

### 2. Foundation Models Framework
*   **Image Input Support:** LLMs now natively accept images as attachments within prompts, allowing for tasks like image captioning, interior design suggestions, and recipe generation from food photos.
*   **Tool Calling:** Allows LLMs to offload specific tasks to external code (Vision APIs) when the model lacks the capability to process certain inputs (e.g., reading a QR code).

---

## Code Patterns and Techniques

### Tap to Segment Implementation
To segment an object, developers utilize the `GenerateIterativeSegmentationRequest`:
1.  Initialize an `ImageRequestHandler` with the source image.
2.  Define a seed point or area (normalized coordinates, 0 to 1).
3.  Perform the request to generate a `PixelBuffer` mask.
4.  **Refinement:** Update the mask by passing additional points to the existing request to add or subtract regions.

### Tool Calling with Vision
To provide an LLM with Vision capabilities (e.g., a Barcode Reader):
1.  **Define the Tool:** Create a class conforming to the `Tool` protocol, defining input arguments.
2.  **Reference Handling:** When the model requests a tool, use `session.history` to resolve an image reference into an `Attachment` (pixel buffer).
3.  **Registration:** Configure your language model session with the Vision tools.
4.  **Labeling:** Provide descriptive labels for attached images so the model knows which image to pass to the tool.

---

## Practical Takeaways for Developers

*   **Interactive UI:** You can now create highly interactive photo-editing or object-selection features using simple pointer inputs (points/scribbles) without requiring custom ML training.
*   **Hybrid Intelligence:** Don't view Vision and Foundation Models as mutually exclusive. Use **Foundation Models** for high-level semantic tasks (reasoning/captioning) and **Vision** for precise, high-performance, deterministic tasks (OCR, barcodes, segmentation).
*   **Responsive Design:** Use `GenerateObjectnessBasedSaliencyImageRequest` on watchOS to automatically focus on the relevant part of an image, preventing important visual information from being lost on small screens.
*   **Performance:** For real-time video analysis, continue to favor native Vision APIs; for complex, "fuzzy" user queries, utilize the Foundation Models Framework.
*   **Best Practices:**
    *   **Lasso Strokes:** When implementing lasso tools, ensure your stroke width is at least 1% of the total image width for accurate processing.
    *   **Normalization:** Remember that all Vision coordinate systems are normalized (origin at the lower-left).

<!-- resources -->

---

## Resources

- [Segmenting objects using taps, scribbles or rectangles](https://developer.apple.com/documentation/Vision/segmenting-objects-using-taps-scribbles-or-rectangles)
- [Implementing saliency-based image cropping in iOS and watchOS](https://developer.apple.com/documentation/Vision/implementing-saliency-based-image-cropping-in-iOS-and-watchOS)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/237/6/a3bdea1e-5c1d-44bc-8c21-9e1958774bd3/downloads/wwdc2026-237_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/237/6/a3bdea1e-5c1d-44bc-8c21-9e1958774bd3/downloads/wwdc2026-237_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/237/
