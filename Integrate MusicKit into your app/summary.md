# Integrate MusicKit into your app

# Summary: Integrate MusicKit into your app (WWDC 2026)

## Overview
This session provides a comprehensive guide for developers looking to integrate **MusicKit**—a Swift framework for Apple platforms—into their applications. Using a workout app as a practical case study, the presenters demonstrate how to configure an app for music access, prompt users for permissions, enable Apple Music subscriptions, allow music selection via a unified interface, and implement robust playback controls.

---

## Key Concepts, APIs, and Frameworks

### 1. Framework & Setup
*   **MusicKit:** A framework built for Swift concurrency and SwiftUI, designed to handle both local library content and the Apple Music catalog.
*   **Developer Token:** Required for MusicKit requests. Developers must register their App ID, enable the MusicKit capability in the developer portal, and ensure the same account is signed in within Xcode.
*   **Media Library Capability:** Must be enabled in the "Signing & Capabilities" tab of Xcode to request user authorization.

### 2. Music Items & Requests
*   **MusicItem:** The fundamental building block of the API. These objects (e.g., Songs, Albums, Playlists) contain attributes (metadata like titles/ratings) and relationships (linked content).
*   **MusicCatalogResourceRequest:** Allows developers to fetch specific content from the Apple Music catalog. It supports `FindEquivalence` (to handle region-specific or explicit-content-filtered resources) and supports pagination via `MusicItemCollection`.

### 3. Playback
*   **MusicPlayer:** The base class for `SystemMusicPlayer` (controls the system Music app, persists playback in background/quitting) and `ApplicationMusicPlayer` (full read/write control over the queue within the app).
*   **Cue:** A collection of playable music items set on a `MusicPlayer`. 
*   **Buffering:** Using `PrepareToPlay` helps reduce latency before starting playback.

---

## Code Patterns & Techniques

*   **Authorization Flow:** Use `MusicAuthorization.request()` to trigger the system alert. The description in the alert is customizable via the Media Library capability settings in Xcode.
*   **Subscription Handling:** Use the `MusicSubscriptionOffer` view modifier to allow users to subscribe to Apple Music without leaving the app. You can pass an options struct to configure UI/messaging and include information for the *Apple Services Performance Partner Program* to earn commissions.
*   **Music Picker:** The `MusicPicker` SwiftUI modifier provides a pre-built, unified UI for users to browse and select music. It supports both single and multi-selection (by passing an array). If the user isn't subscribed, the picker intelligently restricts results to the local library.
*   **Observation:** The playback state and queue are observable objects that can be bound directly to SwiftUI views to drive UI updates (e.g., showing artwork or handling play/pause toggles).
*   **Backgrounding:** For `ApplicationMusicPlayer` to continue playback when the app is backgrounded, you must enable the "Audio Background Mode" capability in Xcode.

---

## Practical Takeaways for Developers

*   **Unified UI:** Leverage the `MusicPicker` rather than building custom search interfaces to provide a familiar and feature-rich experience for users with minimal effort.
*   **Efficient Loading:** Use container-based `Cue` initializers (for albums/playlists) to allow the player to load items lazily, improving start-up performance.
*   **Resource Equivalency:** Always use the `FindEquivalence` option when making catalog requests to ensure your app gracefully handles scenarios where content might be unavailable due to region restrictions or user settings (like explicit content filters).
*   **User History:** Remember that `effectsListeningHistory` defaults to `true`. Respect user intent by keeping this enabled unless there is a specific, valid reason to exclude the app's playback from their recently played shelf.
*   **Documentation:** For advanced use cases, the speakers recommend reviewing *Explore More Content with MusicKit* (WWDC 2022) for library modification and *Meet Apple Music API and MusicKit* for Android/Web integration.

<!-- resources -->

---

## Resources

- [Integrating MusicKit into your app](https://developer.apple.com/documentation/MusicKit/integrating-musickit-into-your-app)
- [Apple Services Performance Partner Program](https://performance-partners.apple.com/home)
- [MusicKit](https://developer.apple.com/documentation/musickit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/254/5/d4b2c60a-8a2a-41d1-a55a-0fd60d927798/downloads/wwdc2026-254_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/254/5/d4b2c60a-8a2a-41d1-a55a-0fd60d927798/downloads/wwdc2026-254_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/254/
