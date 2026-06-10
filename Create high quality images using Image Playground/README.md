# Create high quality images using Image Playground

## Session Summary: Create High-Quality Images with Image Playground

This WWDC 2026 session introduces the **Image Playground framework**, an Apple-provided solution that allows developers to integrate high-quality generative AI image creation directly into their iOS, iPadOS, macOS, and visionOS apps. Built on Apple Intelligence and powered by Private Cloud Compute, the framework provides a seamless, consistent, and privacy-preserving experience for users. The session demonstrates how developers can leverage powerful image models—capable of everything from photorealistic to stylized illustrations—without the need to manage infrastructure, provisioning, or complex model deployments.

---

### Key Concepts and Frameworks
*   **Image Playground Framework (`imageplayground.framework`):** A new, high-level framework that replaces the deprecated `Image Creator` API. It handles all UI, model interaction, and style selection internally.
*   **Private Cloud Compute:** The infrastructure used to process image requests. It ensures that user data is never stored or shared, even with Apple, maintaining a strict privacy-preserving standard.
*   **Adaptive Image Glyphs:** A specialized format used for "Genmoji" style creation that allows generated images to be embedded inline with text, similar to standard emojis.
*   **Usage Limits:** The system manages generation limits on behalf of the developer. Users with iCloud+ subscriptions receive increased access, eliminating the need for developers to build custom "usage-tracking" UI.
*   **Device Availability:** The framework is available on all devices that support Apple Intelligence.

---

### Code Patterns and Techniques
The session emphasizes a "modifier-first" approach for SwiftUI, keeping implementation simple and concise:

*   **SwiftUI Integration:** Adding the `imagePlaygroundSheet` modifier to a button is the primary way to present the interface. It requires only a binding to a Boolean state.
    *   *Completion Handling:* Once a user confirms an image, the completion closure returns a `URL` to a temporary file in the app’s container, which developers must move to permanent storage.
*   **Contextual Seeding:** Developers can "prime" the sheet with context using factory methods:
    *   `.text(description)` and `.extracted(longText)`: These seed the generator with relevant themes or concepts from the app.
    *   **Visual Inspiration:** You can pass an existing `Image` or a `PKDrawing` (Pencil Kit) as a source to guide the model’s composition.
*   **Customization:**
    *   **Options & Styles:** Developers can configure the `ImagePlaygroundOptions` to set desired aspect ratios (e.g., landscape, portrait, square) and restrict the style picker using `allowedList`.
    *   **Personalization:** Enabled by default, this allows users to include people from their photo library. It can be disabled via `Options.Personalization = .disabled`.
    *   **External Providers:** By adding `ExternalProvider` to the allowed styles list, apps can support third-party models (like GPT) if the user has them configured in their system settings.
*   **Environment Checking:** The `SupportsImageGeneration` environment value is used to check for device capability, regional support, and user settings, allowing developers to implement a clean fallback (e.g., falling back to a standard photo picker) when image generation is unavailable.

---

### Practical Takeaways for Developers
*   **Infrastructure-Free:** There are no server endpoints to maintain, API keys to manage, or SDK initializations required. Apple manages the heavy lifting.
*   **Deprecated APIs:** If your app currently uses `Image Creator`, you must migrate to the new `Image Playground` API to maintain functionality and access the latest quality improvements and privacy features.
*   **Graceful Fallbacks:** Always use the `SupportsImageGeneration` environment value to provide an alternative user experience on unsupported devices or regions.
*   **UX Consistency:** The framework provides the exact same sheet/interface as the native Image Playground app, ensuring a familiar experience for the user regardless of which app they are using.
*   **Think Contextually:** The most effective integrations use the "app's unique knowledge" (previous messages, user-specific themes, or existing photos) to seed the model, resulting in images that feel custom-made for the user’s current intent.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/375/
