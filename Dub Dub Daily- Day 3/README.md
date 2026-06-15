# Dub Dub Daily: Day 3

### WWDC 2026: Dub Dub Daily (Day 3) – Summary

#### Overview
Day 3 of WWDC 2026 focused on the evolution of **Apple Intelligence** and the expanded capabilities for developers to integrate intelligent features into their applications. Lori Highland-Show, Senior Engineering Manager for Swift Intelligence Frameworks, joined the show to discuss how Apple is evolving from simple on-device processing to a hybrid model that leverages Private Cloud Compute and third-party foundation models. The core theme of the session is the "partnership" between system-level intelligence and app-level data, enabling developers to build more context-aware, powerful, and seamless user experiences.

---

#### Key Concepts & Frameworks

*   **Foundation Models Framework:** Originally introduced to provide access to on-device models, this year it has been significantly expanded. It now supports:
    *   **Private Cloud Compute:** Access to the powerful server-side models that power Apple Intelligence.
    *   **Third-Party Model Integration:** Official support for integrating models like Gemini and Claude (Anthropic).
    *   **Vision Framework Integration:** Core tools like OCR and barcode scanners are now callable directly from the Foundation Models Framework, allowing models to process visual data more effectively.
*   **Dynamic Profiles:** A foundational technology that allows developers to define conditional paths for intelligence. Instead of using a static prompt or model, developers can route logic dynamically—sending simpler, privacy-sensitive tasks to on-device models and complex, compute-heavy tasks to Private Cloud Compute or third-party models, all within the same application.
*   **App Intents:** The primary mechanism for integrating app functionality with Siri. By adopting App Intents, developers allow users to use natural language to query, find, and take action on information stored within their apps.
*   **Skills:** A new abstraction layer (built on top of dynamic profiles) that allows developers to create and package intelligent "skills," enabling modular, reusable logic for specialized tasks.

---

#### Coding Patterns & Techniques

*   **Hybrid Intelligence Architecture:** Developers are encouraged to move away from "one-size-fits-all" prompts. The recommended pattern is to utilize **Dynamic Profiles** to handle logical branching.
    *   *Example:* An app can use an on-device model for quick, private data sorting, but switch to a cloud-based foundation model when the user requests a deep-dive analysis or summary that requires larger context windows.
*   **Intent-Driven Discovery:** To make an app "Siri-ready," developers should map app functions to the latest **App Intents schemas**. This allows Siri to understand the context of the user’s request—such as retrieving specific data points from an internal database or triggering a specific action—without the user needing to navigate to the app manually.

---

#### Practical Takeaways for Developers

1.  **Adopt App Intents:** If you want your app’s data to be "discoverable" and "actionable" via Siri, prioritizing the implementation of the latest App Intents schemas is the highest-value task for developers this year.
2.  **Optimize for Privacy and Power:** Use the on-device Foundation Model for tasks requiring low latency and high privacy. Reserve Private Cloud Compute or third-party models for complex reasoning tasks that exceed the hardware capabilities of the local device.
3.  **Explore the Documentation:** Comprehensive documentation, API references, and sample code for the updated Foundation Models Framework and App Intents are now live at [developer.apple.com/documentation](https://developer.apple.com/documentation).
4.  **Engage with Experts:** Apple engineers are actively monitoring the **Apple Developer Forums** throughout the week to answer implementation-specific questions regarding the new Intelligence frameworks.
5.  **Utilize Group Labs:** Developers are encouraged to sign up for specific "Group Labs" via the Apple Developer app to receive hands-on guidance from Apple staff on integrating these new tools.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/398/2/410c7536-3689-45c3-a343-661e3cdd641f/downloads/wwdc2026-398_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/398/2/410c7536-3689-45c3-a343-661e3cdd641f/downloads/wwdc2026-398_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/398/
