# Live Activities essentials

# WWDC 2026 Session Summary: Live Activities Essentials

## Session Overview
This session provides a comprehensive guide to implementing **Live Activities**, which allow apps to deliver real-time, glanceable updates on the Lock Screen, the Dynamic Island, Standby mode, Apple Watch (Smart Stack), macOS menu bar, and CarPlay. The presenter, Adi, outlines the entire lifecycle of a Live Activity: from planning the data model and designing UI components using SwiftUI, to managing updates via ActivityKit and push notifications, and finally optimizing for various device form factors and orientations.

---

## Key Concepts, APIs, and Frameworks

*   **ActivityKit:** The core framework for managing the lifecycle of Live Activities, including starting, updating, and ending them.
*   **WidgetKit:** Used to build the UI for Live Activities. Developers define an `ActivityConfiguration` to represent the UI across different states.
*   **Data Modeling:** Live Activities separate data into two categories:
    *   **Static Data (`ActivityAttributes`):** Information that doesn't change (e.g., order ID, coffee shop name).
    *   **Dynamic Data (`ContentState`):** Information that changes over time (e.g., status, time remaining).
*   **Presentation Contexts:**
    *   **Dynamic Island:** Includes Compact (Leading/Trailing), Minimal, and Expanded views.
    *   **Lock Screen & Standby:** Expanded views that can be further customized for landscape or charging states.
    *   **Cross-Device:** Automatic forwarding to Apple Watch, macOS, and CarPlay.

---

## Code Patterns and Techniques

*   **Defining the Data Model:** Developers must define a struct conforming to `ActivityAttributes` for static properties and a nested struct for the `ContentState` (dynamic properties).
*   **Handling View Layouts:** 
    *   Use the `context` parameter in `ActivityConfiguration` to access the most recent `ContentState`.
    *   **Environment Values:** The session highlights using `isDynamicIslandLimitedInWidth` to adjust layouts for landscape vs. portrait, and `activityFamily` to detect and customize the "small" family used on Watch/CarPlay.
    *   **Background Management:** Use `showsWidgetContainerBackground` to detect if the activity is on the Lock Screen versus using `activityBackgroundTint` for a cleaner look in Standby.
*   **Updating Strategies:**
    *   **Foreground:** Call the `update()` method directly via ActivityKit.
    *   **Background (Broadcast):** Best for high-volume apps; subscribers listen to a server-side broadcast channel.
    *   **Background (Push):** Standard push notifications target individual devices using a specific push token.
*   **Interactivity:** Developers can add `AppIntent` to UI buttons. When triggered, the intent executes logic (e.g., recording a rating) even if the app is in the background.

---

## Practical Takeaways for Developers

1.  **Prioritize Glanceability:** Design for the most critical information first. Use the "Minimal" Dynamic Island view to show the absolute most important data point (e.g., a timer gauge).
2.  **Plan for Constraints:** Don't assume your UI will always have the same width. Test across portrait, landscape, and limited-width environments to ensure the UI remains readable.
3.  **Efficient Data Management:** Always define a `staleDate` in your content state to signal to the system when information is no longer current.
4.  **Adopt the "Small" Family:** To ensure your app looks great on Apple Watch and CarPlay, explicitly support the `.small` activity family and provide a customized, simplified view for those contexts.
5.  **Utilize App Intents:** Enhance user engagement by adding buttons for quick actions (e.g., "Rate Order") directly within the Live Activity, reducing the need for users to open the full app.
6.  **Human Interface Guidelines (HIG):** Always cross-reference the official Apple design guidelines to ensure that your Live Activity feels native and consistent with the OS.

<!-- resources -->

---

## Resources

- [Human Interface Guidelines: Live Activities](https://developer.apple.com/design/human-interface-guidelines/live-activities)
- [Starting and updating Live Activities with ActivityKit push notifications](https://developer.apple.com/documentation/ActivityKit/starting-and-updating-live-activities-with-activitykit-push-notifications)
- [ActivityKit](https://developer.apple.com/documentation/ActivityKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/223/4/9098c495-ea8b-44f9-b852-f6eb64840161/downloads/wwdc2026-223_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/223/4/9098c495-ea8b-44f9-b852-f6eb64840161/downloads/wwdc2026-223_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/223/
