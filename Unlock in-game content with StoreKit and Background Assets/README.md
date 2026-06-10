# Unlock in-game content with StoreKit and Background Assets

# Summary: Unlock In-Game Content with StoreKit and Background Assets (WWDC 2026)

This session introduces new enhancements to Apple’s **Managed Background Assets** and announces the release of official **Unity plugins** for both Background Assets and StoreKit. These updates are designed to help game developers reduce app size, streamline content delivery, and integrate native Apple commerce features directly into Unity projects.

---

### Key Concepts, APIs, and Frameworks

*   **Managed Background Assets (iOS/iPadOS/macOS/tvOS/visionOS 27):** A system that automatically manages the downloading and storage of large assets (audio, textures, ML models). It now supports up to 200GB of Apple-hosted content per app.
*   **Localized Asset Packs:** A new feature allowing the system to deliver asset packs based on the user's preferred language. If a specific language pack isn't available, the system performs a "fallback" to regional variants or the primary app language.
*   **Unity Plugins (Apple-Supported):** Two new official plugins available on GitHub that provide a C# bridge to native Apple frameworks:
    *   **Background Assets Plugin:** Handles asset lifecycle and download progress reporting within Unity.
    *   **StoreKit Plugin:** Enables native in-app purchasing flows (merchandising, payment sheets, and transaction verification).
*   **Steam Depot Converter:** A command-line tool (`xcrun.ba.package.convert`) for macOS that converts existing Steam depots into Apple-compatible asset packs.

---

### Code Patterns & Techniques

#### 1. Localizing Asset Packs
To implement localized assets, developers must update their asset pack **JSON manifests** to include language tags. The system handles the logic of matching user settings to the correct bundle automatically.

#### 2. Unity StoreKit Workflow
The session demonstrates a standard C# pattern for handling purchases:
*   **Fetching:** Use the `Product` API to display items.
*   **Purchasing:** Call the `Purchase` API to trigger the native system payment sheet.
*   **Verification:** After a transaction result is returned, verify the transaction before granting content access.
*   **Completion:** Always call `Finish` on verified transactions to close the lifecycle.
*   **Entitlements:** For non-consumables/subscriptions, use `CurrentEntitlements` as the source of truth, as it automatically filters revoked or expired transactions.

#### 3. Development & Testing
*   **Background Assets Mock Server:** When testing in Xcode 27, developers can point to a local folder of packaged assets. The mock server will attach to the debug session to simulate real-world background downloads.
*   **StoreKit Configuration:** Use a local `StoreKit` configuration file in Xcode to test products, subscriptions, and purchase flows without needing live App Store connectivity.

---

### Practical Takeaways for Developers

*   **Reduce App Binary Size:** By shifting heavy assets to Managed Background Assets, you can keep the initial download footprint small and fetch content only when needed.
*   **Adopt Official Plugins:** Instead of relying on third-party wrappers, use Apple’s official C# plugins for better stability, feature parity with native updates, and easier integration.
*   **Prepare for "Apple Games":** Leverage the new ability to add specific images and videos for search results and the "Apple Games" app to increase discoverability.
*   **Landscape Compatibility:** The redesigned system payment sheet in iOS 27 is optimized for landscape mode, ensuring that in-game commerce does not disrupt the user experience.
*   **Migration:** If you are a cross-platform developer, use the provided `xcrun` command-line tools to transition existing Steam assets to Apple’s managed delivery system, significantly reducing the overhead of manual asset management.

<!-- resources -->

---

## Resources

- [Apple Unity Plug-Ins on GitHub](https://github.com/apple/unityplugins)
- [Background Assets](https://developer.apple.com/documentation/BackgroundAssets)
- [StoreKit](https://developer.apple.com/documentation/StoreKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/378/6/16c93f95-21e8-4f7f-bb96-2b3c682fa6c7/downloads/wwdc2026-378_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/378/6/16c93f95-21e8-4f7f-bb96-2b3c682fa6c7/downloads/wwdc2026-378_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/378/
