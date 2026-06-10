# Get the most out of Device Hub

# WWDC 2026: Get the most out of Device Hub

## Session Overview
This session introduces **Device Hub**, a powerful, standalone application shipping with Xcode 27 designed to streamline the management, configuration, and interaction with physical devices and simulators. Moving away from the scattered tools of the past, Device Hub serves as a centralized "control center" for developers. Whether you are conducting UI layout testing, investigating performance issues, or managing a large inventory of hardware, the app provides a unified interface to control device states, manage app data, and capture system-level diagnostics.

---

## Key Concepts

### 1. Unified Device Management
Device Hub treats physical devices and simulators as first-class, identical citizens. It provides a consistent set of tools regardless of the hardware target, allowing for seamless workflow transitions between physical testing and virtual simulation.

### 2. Interface Modes
*   **Compact Mode:** A lightweight, focused window showing a live interactive view of the screen. It features contextual controls (e.g., Digital Crown for Apple Watch, environment controls for Vision Pro) and is ideal for quick checks.
*   **Full Window:** Provides the "Canvas" for interaction and an "Inspector" area for granular configuration and diagnostics.

### 3. The Canvas
The central interaction area where developers can:
*   Use native trackpad gestures, scrolling, and clicks to interact with the device.
*   Toggle between physical dimensions and freely resizable modes.
*   **Capture Keyboard:** Redirects Mac keystrokes directly to the device to test hardware keyboard support and shortcuts.

### 4. Inspector Panels
The Inspector (on the right) houses five specialized panels for deep device configuration:
*   **Device Settings:** Toggle appearance (Dark/Light mode), dynamic text sizes, and location services instantly.
*   **Diagnostics:** Access logs, crashes, and "spin" reports for debugging.
*   **Device Info:** Storage, model, and serial number information.
*   **Apps:** Installation management, uninstallation, and direct manipulation of **app data containers** (downloading/replacing state).
*   **Profiles:** Manage configuration and provisioning profiles via simple drag-and-drop.

---

## Demonstrated Techniques
*   **Reproducing Bugs Across Environments:** The session demonstrates capturing a "state" from a physical device—including screenshots, app data containers, and SysDiagnose logs—and importing them into a simulator. By matching configuration settings (orientation, location, and text size) in the Inspector, developers can reliably reproduce edge-case UI bugs.
*   **Pairing Workflow:** The session highlights a streamlined pairing process for wireless devices, where developers can trigger a pairing request via the sidebar, enter a pin code, and have the device appear instantly in the inventory.
*   **CI/Automation via `devicecuttle`:** For non-GUI workflows, the session introduces `devicecuttle`, a command-line tool that mirrors the capabilities of Device Hub. It supports JSON output, making it ideal for integration into CI/CD pipelines to manage device lists, install builds, or toggle settings programmatically.

---

## Practical Takeaways for Developers
*   **Centralize Your Workflow:** Stop digging through disparate settings menus on physical devices. Use the Device Hub Inspector to toggle complex states like location or text size on the fly.
*   **Data Container Management:** You can now easily extract your app's sandbox data from a device and inject it into a simulator (or vice-versa), making it significantly easier to debug issues tied to specific user data.
*   **Adopt `devicecuttle` for CI:** If your team manages a farm of test devices, shift your shell scripts to use `devicecuttle` for consistent, reliable device interactions.
*   **Real-World UI Testing:** Use the "Capture Keyboard" feature and the "One-to-One" physical size scaling in the Canvas to ensure your UI is accessible and functional at real-world dimensions before submitting to the App Store.
*   **Resources:** Developers are encouraged to check the session "Modernize your UIKit app" for deeper insights into how Device Hub handles resizable UI testing.

<!-- resources -->

---

## Resources

- [Device Hub](https://developer.apple.com/documentation/Xcode/device-hub)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/260/4/87d4b48f-1dfb-413f-a4f8-44d80b0f3432/downloads/wwdc2026-260_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/260/4/87d4b48f-1dfb-413f-a4f8-44d80b0f3432/downloads/wwdc2026-260_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/260/
