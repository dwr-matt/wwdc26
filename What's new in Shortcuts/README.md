# What's new in Shortcuts

This summary covers the WWDC 2026 session "What's new in Shortcuts," which focuses on enhancing how developers can integrate their apps into the Shortcuts ecosystem through improved automation, LLM integration, and persistent data storage.

### Session Overview
The session details significant upgrades to the Shortcuts experience, moving automation settings directly into the editor and introducing new triggers. It also covers enhancements to the `Use Model` action, allowing for better interaction with Apple Intelligence, and the introduction of a new storage system that enables developers to persist data across shortcut runs and synchronize it across devices.

### Key Concepts and APIs
*   **Enhanced Automations:** Automations now reside directly within the Shortcuts editor. Three new trigger types are available:
    *   **Screenshot:** Runs when a screenshot is saved.
    *   **Keyboard:** Runs when an external keyboard connects/disconnects.
    *   **Notification:** Runs in response to a specific notification from an app.
*   **`Use Model` Action:** Leverages Apple Intelligence to process content. It can now access the web for real-time information and process complex app data.
*   **Storage Framework:** A new system that allows developers to save values (strings, lists, or custom App Entities) that persist across shortcut runs.
    *   **Local Storage:** Scoped to a specific shortcut.
    *   **Global Storage:** Shared across multiple shortcuts, ideal for credentials like API keys.
*   **Syncing:** Stored values automatically synchronize via iCloud across a user’s devices.

### Demonstrated Techniques & Patterns
*   **Notification Filtering:** Developers are encouraged to use specific, concise, and actionable notifications. Users can filter these triggers using keywords (e.g., "arriving") to ensure automations only fire on relevant events.
*   **Debugging `Use Model`:** To troubleshoot LLM outputs, developers can use the **Show Content** action to inspect the raw **Model Transcript**. This reveals exactly what data/entities were passed to the model, allowing developers to identify missing properties that might be causing poor logic.
*   **Entity Design for LLMs:** To help models make better decisions, developers should expose rich properties in their `App Intents`. For example, adding an `Ingredients` array to a `Soup` entity provides the necessary context for an LLM to determine "spice level," which a simple name field could not.
*   **Stable Identifiers:** When using storage with custom `App Entities`, developers must use a consistent, stable identifier (such as a database row ID) rather than a device-specific ID. This ensures that an entity stored on an iPhone is correctly recognized by an app running on an iPad.

### Practical Takeaways for Developers
1.  **Refine your Notifications:** Follow the Human Interface Guidelines for notifications. Providing clear, structured information in your notifications empowers users to build sophisticated personal automations using your app.
2.  **Optimize App Entities:** Ensure your `App Intents` expose sufficient detail. If an LLM is struggling to categorize or process your app's content, look at the Model Transcript to see if your entities are missing the necessary metadata.
3.  **Implement Persistent Context:** Use the new Storage API to make your shortcuts "stateful." Instead of building static tools, store history or preferences (like a list of previous selections) to allow your shortcuts to provide more intelligent, non-repetitive results.
4.  **Prioritize Sync Reliability:** When building for cross-device support, ensure that your `App Entity` identifiers are globally unique and consistent to prevent data mismatch errors when a user switches between devices.

<!-- resources -->

---

## Resources

- [Shortcuts](https://developer.apple.com/shortcuts/)
- [Notifications](https://developer.apple.com/design/Human-Interface-Guidelines/notifications)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/310/4/50ce70ab-88da-49ff-8c57-d9136d231e76/downloads/wwdc2026-310_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/310/4/50ce70ab-88da-49ff-8c57-d9136d231e76/downloads/wwdc2026-310_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/310/
