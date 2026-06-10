# Create live communication experiences

# WWDC 2026: Create Live Communication Experiences

## Session Overview
This session introduces the **Live Communication Kit**, a modern framework designed to provide a rich, native, and system-integrated UI for real-time communication apps. By adopting this framework, developers can ensure their applications provide a consistent, professional experience across the entire Apple ecosystem, including full-screen lock screen presentations, the Dynamic Island, Phone app "Recents," and Siri integration. The session focuses on moving away from the legacy `CXProvider` API toward a more flexible, event-driven architecture that manages conversation lifecycles from initiation to termination.

---

## Key Concepts, APIs, and Frameworks

### Core Architecture
*   **Conversation Manager:** The central object responsible for managing the state and lifecycle of all real-time interactions. It communicates with the system via a delegate pattern.
*   **Conversation Lifecycle:** A conversation progresses through defined states—`idle`, `joining`, `joined`, `leaving`, and `left`.
*   **Handles:** Identifiers for participants (Phone number, Email, or generic string). Proper labeling allows the system to match these to saved contacts for profile photos and names.
*   **Capabilities:** A declarative system that defines what a conversation can do (e.g., `video`, `pausing`, `merging`). The system dynamically updates the UI controls based on these capabilities.
*   **PushKit:** Essential for waking backgrounded apps when an incoming conversation arrives.

---

## Code Patterns and Techniques

### The Manager-Delegate Pattern
The framework uses a unified delegate method, `performAction`, for both system-initiated events (e.g., user answers a call from the lock screen) and app-initiated events.
*   **Action Routing:** Developers use a `switch` statement inside the delegate to handle different actions (`join`, `end`, `merge`, `unmerge`). This creates a single, consistent code path, preventing state synchronization issues between the app and the system.

### Conversation Updates
Instead of direct state management, developers report changes via `ConversationUpdate` objects. 
*   **Async Handling:** Because media setup (server connection, stream configuration) is asynchronous, developers are encouraged to wrap these operations in `Tasks` to keep the delegate responsive while reporting updates to the `ConversationManager`.

### Managing Group Conversations
*   **Membership:** The framework distinguishes between `members` (total invited participants) and `active remote members` (those currently streaming media).
*   **Merging:** By declaring the `merging` capability, developers can allow users to combine two separate conversations. The `merge` action carries unique identifiers for both original conversations, which the app uses to aggregate media streams on the backend before updating the UI.

---

## Practical Takeaways for Developers

*   **Migration:** If your app currently relies on the `CXProvider` API, you should plan to migrate to the `Live Communication Kit` for better flexibility and feature support.
*   **Native Integration:** By properly configuring your `ConversationManager` (providing ringtones, icons, and supporting `App Intents`), your app will automatically appear in "Recents" and become compatible with system-level features like redialing via Spotlight.
*   **Stable Identity:** Replace transient session tokens with stable `Handle` values. This ensures that users can successfully redial contacts from their history.
*   **Background Modes:** Ensure your app has the "Audio" and "Voice over IP" background modes enabled in your Xcode project settings to maintain conversations while the app is backgrounded or the device is locked.
*   **Donating Intents:** Beyond basic integration, explicitly donate `App Intents` at the end of conversations to improve discoverability and provide a better experience for Siri-based conversation starting.

<!-- resources -->

---

## Resources

- [Initiating VoIP conversations with LiveCommunicationKit](https://developer.apple.com/documentation/LiveCommunicationKit/initiating-voip-conversations-with-livecommunicationkit)
- [Responding to VoIP Notifications from PushKit](https://developer.apple.com/documentation/PushKit/responding-to-voip-notifications-from-pushkit)
- [LiveCommunicationKit](https://developer.apple.com/documentation/LiveCommunicationKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/226/4/f8343d5b-0c78-4396-be05-956666fb4ae0/downloads/wwdc2026-226_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/226/4/f8343d5b-0c78-4396-be05-956666fb4ae0/downloads/wwdc2026-226_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/226/
