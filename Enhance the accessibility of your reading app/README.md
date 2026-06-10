# Enhance the accessibility of your reading app

# WWDC 2026: Enhance the Accessibility of Your Reading App

## Overview
This session focuses on optimizing long-form reading applications for Apple platforms to ensure they provide a fluid, accessible experience for users of assistive technologies like VoiceOver and SpeakScreen. The presenter, Josh from the accessibility team, emphasizes that reading is a continuous, linear experience that differs significantly from standard UI navigation. The session guides developers through auditing their apps, leveraging built-in framework capabilities, and implementing advanced protocols to handle custom text rendering.

---

## Key Concepts, APIs, and Frameworks

### Core Assistive Technologies
*   **VoiceOver:** A screen reader for blind or low-vision users.
*   **SpeakScreen:** A feature for reading all content on a screen from start to finish.
*   **Accessibility Reader:** A tool introduced in iOS 26 that optimizes text presentation for consumption.

### Relevant Frameworks & Protocols
*   **UIKit (`UITextView`) & SwiftUI (`TextEditor`, `Text`):** The primary, recommended ways to display text, as they offer accessibility features "out of the box."
*   **`UITextInput` Protocol:** A high-fidelity protocol that enables advanced accessibility features (selection, granular navigation) on custom views or non-standard text rendering.
*   **Text Navigation APIs:** Introduced in iOS 18 to allow VoiceOver to move fluidly between distinct UI elements (e.g., jumping from one paragraph view to the next).

---

## Code Patterns & Techniques

### Improving Navigation & Continuity
*   **Linking Elements:** To prevent VoiceOver from getting "stuck" in a single paragraph view, use:
    *   **UIKit:** Implement `accessibilityNextTextNavigationElement` and `accessibilityPreviousTextNavigationElement`.
    *   **SwiftUI:** Use the `.accessibilityLinkedGroup(id:in:)` modifier to link multiple text views, allowing for seamless transition between paragraphs.
*   **Continuous Reading:** Apply the `causesPageTurn` trait to the final text element on a page. When paired with `AccessibilityScroll`, this enables VoiceOver/SpeakScreen to automatically advance to the next page, mimicking an audiobook experience.

### Handling Custom Text
For apps that render custom text (e.g., handwritten notes, PDFs, or specialized typography), the session demonstrates:
*   **Adopting `UITextInput`:** You must implement the full protocol, including:
    *   **`selectionRects(for:)`:** To provide geometry so the system knows where the text is for highlighting and focus.
    *   **Tokenizer:** Essential for managing navigation by word, line, or sentence.
    *   **`UITextInteraction`:** Used to provide visual feedback (handles/highlights) when users select custom text.

### Enhancing Discoverability
*   **Custom Actions:** Use `accessibilityCustomActions` to add specific functionality (like "Save Recommendation") to the **Edit Rotor**. This keeps actions contextually relevant to the text selection.

---

## Practical Takeaways for Developers

1.  **Prioritize Native Views:** Always use `UITextView` or `TextEditor` whenever possible; they are pre-configured with the `UITextInput` protocol and provide most accessibility features by default.
2.  **Audit Your Flow:** Use the **Lines Rotor** and **Read-All** gesture to test your app. If the focus stops at the end of a block or paragraph, your navigation or page-turning implementation needs work.
3.  **Implement Granularity:** Ensure users can navigate by line, word, or character. If your app uses custom views, this is only possible if you fully implement the `UITextInput` protocol and its associated tokenizer.
4.  **Use the Edit Rotor:** If your app allows users to select text to perform actions (save, share, translate), expose those actions via the Edit Rotor rather than just relying on UI buttons, as this is a standard behavior assistive technology users expect.
5.  **Page-Turn Trait:** If your content is paginated, ensure the `causesPageTurn` trait is set correctly to enable a seamless "read-through" experience without manual user intervention.

<!-- resources -->

---

## Resources

- [accessibilityNextTextNavigationElement](https://developer.apple.com/documentation/ObjectiveC/NSObject-swift.class/accessibilityNextTextNavigationElement)
- [editCategory](https://developer.apple.com/documentation/UIKit/UIAccessibilityCustomAction/editCategory)
- [accessibilityLinkedGroup(id:in:)](https://developer.apple.com/documentation/SwiftUI/View/accessibilityLinkedGroup(id:in:))
- [causesPageTurn](https://developer.apple.com/documentation/SwiftUI/AccessibilityTraits/causesPageTurn)
- [UITextInput](https://developer.apple.com/documentation/UIKit/UITextInput)
- [Accessibility for UIKit](https://developer.apple.com/documentation/UIKit/accessibility-for-uikit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/219/4/da70a3a7-e193-4513-904f-991788c1fa81/downloads/wwdc2026-219_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/219/4/da70a3a7-e193-4513-904f-991788c1fa81/downloads/wwdc2026-219_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/219/
