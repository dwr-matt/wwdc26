# Create web extensions for Safari

# WWDC Session Summary: Create Web Extensions for Safari

## Overview
This session provides a comprehensive guide for developers looking to build, test, and distribute web extensions for Safari. Utilizing standard web technologies—HTML, CSS, and JavaScript—developers can create cross-browser compatible extensions. The session demonstrates building a "distraction-blocking" extension from scratch, covering everything from project structure and manifest configuration to advanced features like content blocking, native messaging, and App Store distribution.

---

## Key Concepts, APIs, and Frameworks
*   **Manifest (v3):** The JSON-formatted file serving as the "ID card" for the extension, defining metadata, permissions, and entry points.
*   **Declarative Net Request API:** The primary API for filtering, blocking, or redirecting network requests based on user-defined rules.
*   **Scripting API:** Used for registering and managing content scripts dynamically at runtime.
*   **Storage API:** Enables data persistence (`local` for disk storage, `session` for in-memory) to save user settings and block lists across restarts.
*   **Native Messaging:** A bridge allowing the JavaScript extension to communicate with the containing native Swift/Objective-C app, enabling access to platform-specific features like Biometric Authentication.
*   **Background Pages/Service Workers:** Used to handle extension lifecycle events (like `onInstalled`) and coordinate communication between different parts of the extension.

---

## Demonstrated Code Patterns & Techniques
*   **Dynamic Rule Management:** Instead of static manifest rules, the session demonstrates using `updateDynamicRules` in JavaScript, allowing users to build a custom list of blocked sites at runtime.
*   **Optional Host Permissions:** To ensure user privacy, the extension requests host access at runtime only when needed, rather than requiring broad permissions upfront.
*   **Content Script Injection:** Utilizing `registerContentScripts` with a persistence flag set to `true` allows the injection of UI (like a countdown timer) into web pages dynamically.
*   **Handling Updates:** A background page is used to listen for the `onInstalled` event, ensuring that content scripts and stored data are properly re-initialized after an extension update.
*   **Native Messaging Flow:** 
    1.  **Extension:** Sends a message via `browser.runtime.sendNativeMessage`.
    2.  **App Handler:** The `SafariWebExtensionHandler` in Xcode receives and parses the message.
    3.  **System API:** The native app invokes system-level features (e.g., LocalAuthentication for FaceID/TouchID).
    4.  **Response:** The app sends the result back to the extension.

---

## Practical Takeaways for Developers
*   **Development Workflow:** Use the "Develop" menu in Safari settings to load temporary, unsigned extensions for rapid iteration.
*   **Standardization:** Apple actively participates in the W3C Web Extensions Working Group. If you have an extension for another browser, it is likely highly compatible with Safari with minimal adjustments.
*   **Distribution Options:**
    *   **App Store Connect:** You can create and package your extension directly in a web browser using the Safari Web Extension Packager, or use Xcode for more advanced native integration.
    *   **TestFlight:** Always use TestFlight to distribute beta builds for user feedback before the final App Store submission.
*   **Cross-Platform Support:** By using standard web technologies, your extension can run on iOS, iPadOS, macOS, and visionOS from a single codebase.
*   **Privacy First:** Always design extensions to be transparent. Using host permissions to trigger a prompt ensures users are in control of their data, which results in a better user experience and trust.

### Helpful Resources
*   **MDN Web Docs:** Use the [cross-browser documentation](https://developer.mozilla.org) for Web Extensions APIs.
*   **Feedback:** Use [Feedback Assistant](https://feedbackassistant.apple.com) or [bugs.webkit.org](https://bugs.webkit.org) to report issues encountered while developing on Safari.

<!-- resources -->

---

## Resources

- [w3.org — W3C WebExtensions Community Group](https://www.w3.org/community/webextensions/)
- [Packaging and distributing Safari Web Extensions with App Store Connect](https://developer.apple.com/documentation/SafariServices/packaging-and-distributing-safari-web-extensions-with-app-store-connect)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [MDN Web Docs - Web Extensions API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/216/5/4fceecc8-1e28-465c-b894-fd0d03067c18/downloads/wwdc2026-216_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/216/5/4fceecc8-1e28-465c-b894-fd0d03067c18/downloads/wwdc2026-216_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/216/
