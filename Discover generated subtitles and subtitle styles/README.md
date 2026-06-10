# Discover generated subtitles and subtitle styles

# WWDC 2026: Discover Generated Subtitles and Subtitle Styles

## Session Overview
This session introduces powerful new accessibility features for media playback in iOS and macOS: **System-generated subtitles** and **in-playback subtitle style customization**. These features are designed to improve content accessibility by providing real-time, on-device transcription and translation for media that lacks localized subtitles. The session also highlights how developers can leverage system APIs to provide users with a seamless previewing experience when choosing subtitle styles.

---

## Key Concepts

### 1. Generated Subtitles
*   **On-Device Generation:** Apple now provides AI-generated subtitles created locally on the device during playback.
*   **Types:**
    *   **Speech Transcription:** Converts audio to text in the same language.
    *   **Language Translation:** Translates existing (authored) subtitles into a different language.
*   **Availability:** These features are available automatically for HLS streams, VOD, and file-based content (including app-bundled media). They require no custom implementation to function, provided the app uses standard AV Foundation playback controls.
*   **Designation:** Generated subtitles are marked in the UI with a "sparkle" icon and a "Translated" label to distinguish them from authored, human-provided subtitles.

### 2. Subtitle Style Preview
*   Users can now customize their subtitle appearance (font, color, size, border) directly within the video player UI rather than navigating to the system Settings app.
*   The system provides a real-time preview of these styles so users can see how their choice affects readability before committing.

---

## APIs and Frameworks
*   **AVFoundation:** The primary framework powering these features.
*   **`AVPlayerViewController` / `AVPlayerView`:** The easiest implementation path. These provide full UI for subtitle selection and style previews out-of-the-box.
*   **`AVLegibleMediaOptions` / `AVLegibleMediaOptionsMenuController`:** Useful for developers who want to integrate the system’s subtitle selection and style preview into custom-built player UIs without implementing the underlying selection logic.
*   **`AVPlayerLayer`:** Offers granular control for developers implementing custom UI to trigger and show style previews.
*   **`AVCaptionRenderer`:** Provides the raw data for style previews for developers building highly custom rendering pipelines (developers must handle the actual drawing).

---

## Implementation Patterns & Techniques

### Subtitle Style Preview with `AVPlayerLayer`
To implement the preview in a custom player, the session suggests the following pattern:
1.  **Fetch Profiles:** Retrieve available system styles using their profile IDs.
2.  **Trigger Preview:** Call the preview API when a user hovers over or selects a style.
    *   Pass the style to render a localized placeholder string.
    *   Set the `text` parameter to `nil` to use system-default preview text.
    *   Use the `position` parameter to offset the preview away from UI controls.
3.  **Hide Existing Subtitles:** The system automatically masks active subtitles during a preview session to prevent visual clutter.
4.  **Finalize Selection:** Once the user selects a style, call the setter to apply it system-wide for the remainder of the session.
5.  **Clean Up:** Stop the preview to restore active subtitles.

---

## Developer Takeaways
*   **Accessibility First:** By adopting standard `AVFoundation` controllers, your app automatically inherits accessibility improvements like generated subtitles without extra code.
*   **Audit Your UI:** If you have built a custom video player, ensure it includes an interface for subtitle selection. If it lacks one, the `AVLegibleMediaOptionsMenuController` is the recommended path to add it quickly.
*   **Enhance Customization:** Implementing the Subtitle Style Preview is a high-value accessibility win. It allows users to iterate on readability settings without leaving the immersive video experience.
*   **Respect Authored Content:** Note that system-generated subtitles are a fallback; the system will always prioritize and preserve authored subtitles if they are available for the selected language.

<!-- resources -->

---

## Resources

- [What's new in HTTP Live Streaming](https://developer.apple.com/streaming/Whats-new-HLS.pdf)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/256/4/d28efb5e-5550-468d-b1d1-caec51ce55e6/downloads/wwdc2026-256_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/256/4/d28efb5e-5550-468d-b1d1-caec51ce55e6/downloads/wwdc2026-256_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/256/
