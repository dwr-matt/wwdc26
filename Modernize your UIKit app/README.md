# Modernize your UIKit app

## Session Summary: Modernize your UIKit app (WWDC 2026)

### Overview
This session focuses on the critical shift toward **full app adaptivity** in iOS 27 and macOS 27. As iPhone apps become fully resizable via iPhone Mirroring on Mac and iPad, developers must move away from fixed-screen assumptions toward a dynamic, window-based layout model. The session outlines the technical requirements for this transition, introduces new UI customization APIs for navigation and tab bars, provides guidance on integrating Apple Intelligence, and debuts a new Xcode "Agentic Coding" skill designed to automate much of the modernization workload.

---

### Key Concepts & Frameworks
*   **Dynamic Adaptivity:** Apps must now handle arbitrary window sizes at runtime. References to the "Main Screen" are effectively deprecated.
*   **Scene Lifecycle:** Transitioning from `AppDelegate` to `UISceneDelegate` is now a mandatory requirement for building with the latest SDKs.
*   **User Interface Idiom & Orientation:** The "Phone" vs. "Pad" idiom and explicit interface orientation checks (e.g., `isPortrait`) are no longer reliable for layout logic. Developers must move exclusively to **Size Classes**.
*   **Apple Intelligence Integration:** New APIs allow apps to surface "Ask Siri" options in menus and annotate views with `AppEntities` to provide context-aware data to system intelligence.
*   **Agentic Coding:** A new feature in Xcode 27 that acts as an automated refactoring tool, capable of scanning projects and performing the heavy lifting of modernization (e.g., converting screen references and updating lifecycle management).

---

### Techniques & Code Patterns
*   **Removing Screen References:**
    *   **Don't:** Use `UIScreen.main`.
    *   **Do:** Access the screen dynamically from the `UIWindowScene` context.
    *   **Best Practice:** Remove screen-based logic entirely in favor of `traitCollection` properties or view-based layout geometry.
*   **Trait Tracking:** Use `traitCollection.displayScale` instead of screen scale. The system now uses **Automatic Trait Tracking**, where layout methods (`layoutSubviews`, `drawRect`) are automatically re-invoked when relevant traits change.
*   **Observing Changes:** Where automatic tracking isn't sufficient, use `registerForTraitChanges` to define a closure that triggers cache invalidation or UI updates.
*   **Geometry Management:** Instead of `screen.bounds`, use `UIWindowScene.effectiveGeometry` to monitor available space. 
*   **Motion & Location:** `UIView` now conforms to new Core Motion/Core Location body protocols, ensuring data stays consistent with the device's coordinate space regardless of orientation.

---

### Practical Takeaways for Developers
1.  **Mandatory Migration:** If your app is not yet on the `UIScene` lifecycle, it will fail to launch on the iOS 27 SDK. Prioritize this migration immediately.
2.  **Audit Your Codebase:** Search for `UIScreen`, `interfaceOrientation`, and `userInterfaceIdiom`. These are the primary sources of potential bugs in a resizable environment.
3.  **UI/UX Refinements:**
    *   **Tab Bars:** Explore the `sidebar` representation on iPhone by setting `tabBarController.sidebar.preferredPlacement`.
    *   **Navigation:** Use `barMinimizationBehavior` to control how navigation bars respond to scroll events.
4.  **Leverage New Tools:**
    *   **DeviceHub:** Use the new "Enter Resize Mode" in Xcode 27 or the DeviceHub app to simulate arbitrary window sizes without needing multiple test devices.
    *   **Xcode Agent:** Utilize the new modernization skill to handle the repetitive refactoring of lifecycle and orientation code. If the code is too complex for the agent, look for the helpful comments it leaves in your source files.
5.  **Test Real-World Scenarios:** Ensure your app functions correctly when moved between displays on an iPad and when mirrored from an iPhone to a Mac, as these environments trigger the most critical edge cases for layout and interaction.

<!-- resources -->

---

## Resources

- [TN3208: Preparing your app’s launch screen to meet App Store requirements](https://developer.apple.com/documentation/Technotes/tn3208-preparing-your-apps-launch-screen-to-meet-app-store-requirements)
- [TN3210: Optimizing your app for iPhone Mirroring](https://developer.apple.com/documentation/Technotes/tn3210-optimizing-your-app-for-iphone-mirroring)
- [Make your UIKit app more flexible](https://developer.apple.com/videos/play/wwdc2025/282/)
- [Adapting your app when traits change](https://developer.apple.com/documentation/UIKit/adapting-your-app-when-traits-change)
- [Transitioning to the UIKit scene-based life cycle](https://developer.apple.com/documentation/UIKit/transitioning-to-the-uikit-scene-based-life-cycle)
- [Automatic trait tracking](https://developer.apple.com/documentation/UIKit/automatic-trait-tracking)
- [Human Interface Guidelines: Menus](https://developer.apple.com/design/human-interface-guidelines/menus)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/278/4/8c3f2e61-52d3-4915-9543-96e2f13adc8b/downloads/wwdc2026-278_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/278/4/8c3f2e61-52d3-4915-9543-96e2f13adc8b/downloads/wwdc2026-278_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/278/
