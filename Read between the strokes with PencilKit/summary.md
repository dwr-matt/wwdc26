# Read between the strokes with PencilKit

# Session Summary: Read between the strokes with PencilKit

## Overview
This session introduces powerful new capabilities added to **PencilKit** in iOS 27, focused on making handwritten content searchable, recognizable, and programmatically manipulatable. The update transitions PencilKit from a simple drawing canvas into a framework that provides deep access to the underlying data model, allowing developers to integrate handwriting recognition (HWR), path conversion, and advanced stroke geometry operations into their applications.

---

## Key Concepts, APIs, and Frameworks

### 1. Handwriting Recognition (HWR)
PencilKit now offers native, on-device handwriting recognition across iOS, iPadOS, macOS, and visionOS 27.
*   **`PKStrokeRecognizer`**: The primary API for converting strokes into text. It supports 29 languages and uses the system's language settings by default (configurable per app).
*   **Indexable Content**: Provides a searchable string representing the full drawing. Unlike the standard recognition result (which provides the "best" answer), indexable content concatenates all possible candidates, ensuring users can find content regardless of ambiguous handwriting.
*   **Text Search**: Enables developers to search for specific strings within a drawing. It returns arrays of search results, which can be visualized as highlights or used to integrate with the system-wide `UIFindInteraction`.
*   **Accessibility**: HWR data can now be exposed to VoiceOver, allowing screen readers to interpret and speak handwritten notes.

### 2. Path Conversion
PencilKit now supports interoperability between its native **cubic uniform B-splines** and standard **Bezier paths**. 
*   This allows developers to store strokes in a Bezier-based format and reconstruct them in PencilKit without loss of fidelity.
*   Crucially, this allows developers to feed existing custom drawing models into the `PKStrokeRecognizer`, enabling handwriting recognition even in apps that do not use `PKCanvasView`.

### 3. Model Enhancements
*   **Stable Identity**: `PKStroke` and `PKStrokePath` now adopt the `Identifiable` protocol, providing a stable UID. This enables tracking of specific strokes across transforms, edits, and undo/redo operations.
*   **Selection State**: A new method, `canvasViewSelectionDidChange`, allows apps to track and respond to user selection changes in real-time.
*   **Render Group IDs**: Developers can now control stroke compositing by managing render group IDs, allowing for custom blending behavior.

### 4. Stroke Slicing
*   **Programmatic Erasing**: Developers can pass a `PKStrokePath` to act as an eraser, programmatically cutting existing strokes into multiple independent segments (complete with mask data).
*   **Sub-stroke Extraction**: Using subscript access with parametric ranges, developers can extract specific segments of a stroke. This is ideal for animations (e.g., replaying handwriting) or analyzing specific stroke segments for educational apps.

---

## Code Patterns & Techniques
*   **Asynchronous Processing**: Because slicing and complex HWR operations can be computationally expensive, developers are encouraged to run these tasks on background threads to avoid blocking the main UI loop.
*   **Indexing Logic**: When persisting `indexableContent`, store the model version alongside the data. Compare it against the current system version to determine if a re-index is necessary, as HWR models improve over time.
*   **Bezier/PencilKit Interop**: When converting to Bezier paths, remember that Bezier paths do not store PencilKit-specific metadata (opacity, force, ink properties). These must be managed separately and reapplied during reconstruction.

---

## Practical Takeaways for Developers
1.  **Adopt `PKStrokeRecognizer` immediately** to add value to any app involving Apple Pencil input, whether for note-taking or educational feedback.
2.  **Use `UIFindInteraction`** to leverage existing system patterns for finding and highlighting text within your canvas.
3.  **Leverage Stable IDs** to build custom tools that require tracking specific strokes, such as custom selection, object manipulation, or "smart" ink tools.
4.  **Use Sub-stroke Extraction for interactivity**; it allows for high-fidelity animations and granular control over stroke segments that were previously impossible to handle individually.
5.  **Performance Warning**: Keep your `PKCanvasView` responsive by offloading heavy data model manipulations (like large-scale programmatic erasures) to background queues.

<!-- resources -->

---

## Resources

- [Controlling stroke rendering for animation and editing](https://developer.apple.com/documentation/PencilKit/controlling-stroke-rendering-for-animation-and-editing)
- [Recognizing handwriting and converting it to text](https://developer.apple.com/documentation/PencilKit/recognizing-handwriting-and-converting-to-text)
- [Building a handwriting recognition experience with PencilKit](https://developer.apple.com/documentation/PencilKit/building-a-handwriting-recognition-experience-with-pencilkit)
- [PencilKit](https://developer.apple.com/documentation/PencilKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/203/4/eb979cd5-af5b-4091-87ec-4839e8d131b9/downloads/wwdc2026-203_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/203/4/eb979cd5-af5b-4091-87ec-4839e8d131b9/downloads/wwdc2026-203_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/203/
