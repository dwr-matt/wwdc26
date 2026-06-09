# Validate your App Intents adoption with AppIntentsTesting

This WWDC 2026 session introduces **AppIntentsTesting**, a new framework designed to provide robust, automated, and out-of-process testing for App Intents. The session emphasizes moving away from manual testing for system-level integrations—such as Siri, Shortcuts, Spotlight, and Widgets—by providing a dedicated testing layer that ensures core app functionality remains consistent across releases.

### **Overview**
The session demonstrates how to validate the entire App Intents stack without relying on app internals or UI-bound automation. By running tests in an `XCUITest` bundle, developers can exercise intents, queries, and system-level integrations (like Spotlight indexing and View annotations) in an isolated process. This allows for a "test-driven" development approach where developers can verify logic on-device, ensuring that core features are reliable before they ever reach the user.

### **Key Concepts & Frameworks**
*   **AppIntentsTesting:** The core framework that communicates across process boundaries with the app to execute intents, query entities, and verify system state.
*   **Intent Definitions:** The primary interface for testing. It allows access to your app's intents, entities, and queries via bundle identifiers without needing to import your application code.
*   **Test-Only Intents:** Focused, non-discoverable intents (marked with `isDiscoverable = false` and wrapped in `#if DEBUG`) designed purely for setting up test environments or resetting state.
*   **Out-of-Process Execution:** Tests run in the `XCUITest` process, while the app runs in its own process, ensuring zero shared state and preventing reliance on app-specific internal mocks.

### **Code Patterns & Techniques**
*   **Dynamic Member Lookup:** Used to access entity properties (e.g., `result.value.title`) directly from the test runner, simplifying assertions on data returned by intents.
*   **Intent Composition:** Demonstrates chaining multiple intents, such as passing the output of a `CreateEventIntent` directly into an `UpdateEventIntent`, mirroring how users build complex flows in the Shortcuts app.
*   **Automatic Type Conversion:** The framework handles primitive type conversions automatically; for custom types, developers can use `IntentValueConvertibleWrapper`.
*   **Spotlight & View Annotation Testing:** 
    *   **Spotlight:** Using `SpotlightQuery` to ensure that entities are correctly indexed and discoverable through system-wide search.
    *   **View Annotations:** Using `viewAnnotations()` to verify that the app is correctly telling the system which entity is currently displayed on screen, which is critical for context-aware Siri interactions.
*   **Test-Driven Development (TDD):** The workflow encourages writing a test (e.g., for a missing string query), observing a failure, implementing the feature in the app, and confirming the test passes.

### **Practical Takeaways for Developers**
1.  **Eliminate Mocks:** Because these tests run on-device, you are testing the actual code path that users trigger, removing the need for fragile stubs or mocks.
2.  **Continuous Integration:** Since tests live in a standard `XCUITest` bundle and don't depend on UI navigation, they are fast, reliable, and ideal for inclusion in CI pipelines to prevent regressions.
3.  **Encapsulation:** You do not need to compile your app code into the testing target. Simply provide the bundle identifier, allowing for cleaner project architecture.
4.  **Verification Strategy:** 
    *   **Fundamental Layer:** Start by testing the basic actions, data, and queries.
    *   **Integration Layer:** Once the fundamentals are solid, use the framework to test system-level interactions like Spotlight indexing and Siri context (View annotations).
5.  **Preparation:** Download the **Comet Cal** sample project provided by Apple to see the implementation of test-only intents and the complete suite of tests in action.

<!-- resources -->

---

## Resources

- [Testing your App Intents code](https://developer.apple.com/documentation/AppIntentsTesting/testing-your-app-intents-code)
- [App Intents Testing](https://developer.apple.com/documentation/AppIntentsTesting)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/295/4/cdcee6d3-e3e9-4201-b1ef-cd33e2d10e6f/downloads/wwdc2026-295_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/295/4/cdcee6d3-e3e9-4201-b1ef-cd33e2d10e6f/downloads/wwdc2026-295_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/295/
