# Rev up your CarPlay app

# WWDC 2026: Rev up your CarPlay app

## Session Overview
This session introduces the new capabilities coming to CarPlay with iOS 27. It focuses on expanding app categories, improving UI consistency through the CarPlay framework, and introducing sophisticated features for video and navigation apps. Developers are encouraged to leverage new templates and APIs to provide richer, more interactive experiences that keep drivers focused while offering deeper content engagement, such as video browsing and advanced route coordination with vehicle systems.

---

## Key Concepts, APIs, and Frameworks

### 1. New Video Capabilities
*   **Video in Car:** iOS 27 now supports video browsing and playback for apps in vehicles that support the "Video in Car" feature.
*   **Entitlements:** Developers should include both CarPlay audio and CarPlay video entitlements to ensure the app appears in the CarPlay home screen across various vehicle capabilities.
*   **Graceful Degradation:** If video is unavailable, the system automatically transitions playback to audio-only mode.

### 2. UI Enhancements & Framework Templates
*   **Mini-Player:** A new `Now Playing` mini-player allows users to see playback status and perform quick actions (play/pause/skip) without leaving their current screen. 
*   **List & Card Improvements:** Lists now support flexible aspect ratios for thumbnails. New overlays include:
    *   **Sports Overlay:** Displays team names, scores, and event status.
    *   **Custom Badges:** Use images or text overlays to indicate live streaming or "newly added" content.
*   **Details Header:** A new UI component used to display a primary item prominently at the top of a list, combining a thumbnail, text, and action buttons.

### 3. Voice Control Template
*   Available to **all** app categories (previously limited).
*   Can be presented as a full-screen view or as an **overlay** on top of existing templates (like the Map template).
*   Supports action buttons (up to two) to initiate tasks like navigation or phone calls via URL schemes (`CPTemplateApplicationScene`).

### 4. Navigation & Route Sharing
*   **Panels:** A new way to customize the primary interface of the Map template, allowing developers to show custom UI elements (grids, route choices, waypoints) while keeping the map visible.
*   **Route Sharing:** Allows the app to sync route segments with the vehicle. This enables the car to:
    *   Optimize energy consumption (e.g., suggesting charging stops for EVs).
    *   Adjust driver assistance systems (e.g., lane-changing) based on the app's route.

---

## Code Patterns & Techniques
*   **Playback Configuration:** Use `CPPlaybackConfiguration` to provide metadata for items. It is critical to update `elapsedTime`, `duration`, and `playbackAction` whenever the state changes to keep the UI in sync.
*   **App Logic for Video:** Use `CPSessionConfiguration` to check if the vehicle supports video before displaying a "Videos" tab in your UI.
*   **Voice Interactions:** Use `AVAudioSession` with the `playAndRecord` category, default mode, and disable mixing to ensure clear voice feedback/processing sounds.
*   **URL Handling:** Perform tasks like starting navigation by opening a URL via `CPTemplateApplicationScene`.

---

## Practical Takeaways for Developers

*   **Test with CarPlay Simulator:** The simulator is now integrated into the **Device Hub** (Xcode). Use it to test different screen resolutions and vehicle configurations (especially the "Video in Car" feature).
*   **Adopt the Details Header:** Use this template to create a polished, "Now Playing" style experience for specific content (movies, episodes, etc.) to improve discoverability.
*   **Optimize for Safety:** When using voice overlays, ensure text variants are short and readable.
*   **Enable Route Sharing:** If you have a navigation app, opt-in to route sharing to provide a seamless "connected" experience with the vehicle’s internal systems, particularly for EVs.
*   **Check the Docs:** Consult the **CarPlay Developer Guide** for a full breakdown of which templates are permitted for specific app categories.

<!-- resources -->

---

## Resources

- [CarPlay for developers](https://developer.apple.com/carplay)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/212/4/c594f5de-1012-4f5a-bad4-95ca200f5f58/downloads/wwdc2026-212_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/212/4/c594f5de-1012-4f5a-bad4-95ca200f5f58/downloads/wwdc2026-212_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/212/
