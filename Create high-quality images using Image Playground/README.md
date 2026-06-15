# Create high-quality images using Image Playground

# WWDC 2026 Summary: Create high-quality images using Image Playground

## Overview
This session introduces the **Image Playground framework**, a powerful toolset that allows developers to integrate Apple’s generative image models directly into their applications. Designed for iOS, iPadOS, macOS, and visionOS, the framework enables users to create high-quality, photorealistic, or stylized images within the app’s native UI. Apple handles the heavy lifting—including infrastructure, usage limits, and model processing via Private Cloud Compute—allowing developers to focus on integrating the experience into their app’s specific context.

## Key Concepts, APIs, and Frameworks
*   **Image Playground Framework:** The core framework for generating images. It is built on top of Apple Intelligence-supported devices.
*   **Private Cloud Compute:** All generative tasks are processed in a privacy-preserving cloud environment where user data is not stored or shared with Apple.
*   **ImagePlayground Sheet:** The primary UI component for generating images. It provides the full interface for text descriptions, style selection, and user confirmation.
*   **Adaptive Image Glyphs:** A special output format for "Genmoji"-style characters that can be embedded directly into text strings, similar to standard emojis.
*   **External Provider Support:** Developers can opt-in to allow users to use third-party image generation providers (e.g., GPT) if the user has configured them in settings.
*   **Deprecated API:** The previous `Image Creator` API is now deprecated in favor of this new framework.

## Code Patterns and Techniques
*   **SwiftUI Integration:** The framework uses a simple view modifier, `.imagePlaygroundSheet`, attached to a button or view. It takes a binding to a boolean to control visibility.
    ```swift
    .imagePlaygroundSheet(isPresented: $isShowingSheet) { url in
        // Handle result URL
    }
    ```
*   **Context Seeding:** Developers can "prime" the UI by passing `Concept` objects, which pull context from the app (e.g., text descriptions, theme keywords, or existing images) into the generator.
*   **Pencil Kit Integration:** On iPad, developers can pass `ImagePlaygroundConcept.Drawing` (from a `PKDrawing`) to the sheet, using the user's freehand strokes as visual guidance for the model.
*   **Configuration:** Developers can specify `ImagePlaygroundOptions` to control:
    *   **Size/Aspect Ratio:** Maps a `CGSize` to the closest supported resolution.
    *   **Style Control:** Uses `ImagePlaygroundGenerationStyle` to define a default style and a restricted list of allowed styles.
    *   **Personalization:** Toggle the "People Picker" and name detection on or off based on the app's requirements.
*   **Environment Checking:** Use the `supportsImageGeneration` environment value to gracefully handle device, language, and regional limitations.

## Practical Takeaways for Developers
*   **No Infrastructure Required:** You do not need to manage API keys, server endpoints, or provisioning. The system manages usage limits and infrastructure on your behalf.
*   **User Privacy:** The architecture is designed with privacy by default; no data is stored or shared with Apple, as it runs via Private Cloud Compute.
*   **Graceful Degradation:** Use the `supportsImageGeneration` environment flag to show or hide the feature. If it returns `false`, implement a simple fallback (like a local photo picker) to ensure a consistent experience across all devices.
*   **Consistent UX:** By using the standard sheet, your app maintains a consistent look and feel with Apple’s native apps like Messages and Freeform, reducing the need for custom UI development.
*   **Contextual Relevance:** The power of this API lies in how well you "seed" it. The more relevant the metadata (like card messages or themes) you pass into the `Concept` factory methods, the better the output will be for your specific user.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/375/
