# Unwrap PaperKit

# Summary: Unwrap PaperKit (WWDC 2026)

## Overview
This session introduces **PaperKit**, the framework powering canvas experiences across Apple platforms, including Notes, Preview, and Freeform. For the first time, developers can integrate this robust canvas engine into their own applications. The session demonstrates how to manage canvas data models, control user interaction at an element level, and implement custom UI overlays using adornments to create professional-grade, interactive drawing and layout applications.

---

## Key Concepts, APIs, and Frameworks
*   **PaperKit:** The core framework for managing rich canvases (shapes, images, text, and pencil strokes).
*   **PaperMarkup:** The primary data model representing the canvas content. It features a `subElements` property (a `markupOrderedSet`) that allows programmatic reading and writing of every object on the canvas.
*   **Markup Protocol:** The base protocol for all canvas elements, providing shared properties such as `frame`, `rotation`, and `allowedInteractions`.
*   **Adornments:** A new, non-persistent layer for the canvas. Adornments are visual overlays anchored to specific canvas coordinates that track zoom and scroll but are **not** saved or exported with the document.
*   **Integration:** PaperKit is built on **PencilKit**, allowing full access to existing PencilKit model APIs, including new support for character recognition and Bezier path conversion.

---

## Code Patterns and Techniques

### Managing Interaction
Developers can precisely control user behavior via the `allowedInteractions` property on elements.
*   **Fine-grained control:** You can toggle specific capabilities such as moving, resizing, rotating, deleting, or styling.
*   **Read-only:** Setting the interaction property to `.readOnly` is the recommended pattern for "template" elements (e.g., non-editable layout containers).

### Working with Elements
To modify elements programmatically, developers iterate through the `subElements` set. Since each element has a concrete type (e.g., `ShapeElement`, `ImageElement`), you can access type-specific properties like `strokeColor`, `fillColor`, or `cornerRadius`. After modifying these properties, you update the `PaperMarkupViewController` to reflect changes on the canvas.

### Implementing Adornments
Adornments are managed via the `adornments` property on the `PaperMarkupViewController`. 
*   **Lifecycle:** Create an array of `MarkupAdornment` objects and assign them to the controller.
*   **Delegation:** Handle interactions using the `didTapAdornmentWithID` delegate method, which allows you to trigger app-specific flows (e.g., opening an `ImagePlaygroundViewController`) without cluttering the persistent document model.

---

## Practical Takeaways for Developers
1.  **Canvas as a Data Model:** Treat your canvas as a programmatically accessible set of objects. Use `subElements` to build logic around user-generated content.
2.  **Separate UI from Data:** Use **Adornments** for temporary, functional UI elements like buttons or tool-specific handles. This keeps your document file clean by ensuring these UI controls are never saved to the underlying markup.
3.  **Enhanced Interaction:** By utilizing the `markupInteractions` option set, you can create hybrid experiences where some parts of the canvas are "locked" (like comic book panels or static background templates) while others remain fully editable.
4.  **Leverage PencilKit:** Because PaperKit is a superset of PencilKit, existing skills and code involving `PKDrawing` or PencilKit APIs translate directly into this more advanced canvas environment.

<!-- resources -->

---

## Resources

- [PaperKit](https://developer.apple.com/documentation/PaperKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/372/4/012a7de6-cf54-420f-aaf7-02ea568485bf/downloads/wwdc2026-372_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/372/4/012a7de6-cf54-420f-aaf7-02ea568485bf/downloads/wwdc2026-372_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/372/
