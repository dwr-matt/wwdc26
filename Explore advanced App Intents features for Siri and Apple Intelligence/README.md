# Explore advanced App Intents features for Siri and Apple Intelligence

# WWDC 2026: Explore Advanced App Intents for Siri and Apple Intelligence

## Session Overview
This session focuses on elevating the integration between third-party applications and the Apple ecosystem using App Intents. By moving beyond basic functionality, developers can create "polished and personal" experiences where Siri and Apple Intelligence treat an app’s content as a first-class citizen. The talk covers how to tailor conversational responses, provide rich visual context, help the system discover app content via semantic indexing and on-screen awareness, and bridge app entities with existing system integrations like Notifications and Media playback.

---

## Key Concepts, APIs, and Frameworks

### 1. Conversational Customization
*   **Dialogue Responses:** Developers can use the `providesDialog` protocol in the `perform` method to define custom responses that match the app’s personality (e.g., using "mixtape" instead of "playlist").
*   **Dialogue Requests:** Allows Siri to ask the user clarifying questions *during* an intent’s execution (e.g., asking for a label if a user tries to create a duplicate timer).
*   **Entity Display Representation:** Defines how entities appear visually and phonetically across the system (Spotlight, Siri, Shortcuts). This includes support for titles, subtitles, and images.

### 2. Apple Intelligence & Interaction Donations
*   **Interaction Donations:** By donating UI-based actions (via the `IntentDonationManager` API), apps can teach Apple Intelligence about user preferences. This enables the system to infer the correct app/action even when the user hasn't explicitly invoked a specific shortcut.
*   **Confirmation & Ownership:** New `EntityOwnershipProviding` protocols help the system understand if an entity is public or shared. This informs Siri’s decision to request user confirmation before performing actions with side effects, mitigating risks associated with LLM-based interactions.

### 3. Content Discovery & Search
*   **Semantic Indexing:** Using `CSSearchableIndex` to index entities ensures that Siri can perform "semantic searches" (understanding intent based on meaning rather than exact keywords).
*   **Intent Value Query:** Used for non-indexed or highly dynamic content. It receives structured search input from the system and allows the app to return multiple entity types dynamically.
*   **System Search In-App:** Adopting the `systemSearchInApp` schema allows users to perform in-app searches directly through Siri.

### 4. On-Screen Awareness
*   **View Annotations:** Using `NSUserActivity` and `ViewEntity` annotations, developers can bridge the gap between pixels and data. This allows users to reference objects on screen (e.g., "play that one") without naming them.
*   **Collection & Custom Annotations:** APIs like `collection annotations` (for lists) and `CustomCanvasView` annotations ensure the system understands content even in complex or non-standard UI elements.

---

## Code Patterns & Techniques
*   **Performance Optimization:** Implement `displayRepresentation` querying within your `EntityQuery` to allow Siri to "peek" at metadata without loading the entire heavy entity record from the database.
*   **Entity Annotations:** Link app entities to standard system integrations by assigning the `appEntityIdentifier` property to:
    *   `UNMutableNotificationContent` (for notifications).
    *   `MediaSessionRepresentable` (for Now Playing).
    *   `AlarmConfiguration` (for AlarmKit).
*   **Logic Branching:** In the `perform` method, use a donation parameter to differentiate between calls made by the user via the UI versus those triggered via an intent.

---

## Practical Takeaways for Developers
1.  **Start with Display Representations:** Ensure all your app entities have rich visual definitions. This is the single highest-impact change for making an app feel integrated.
2.  **Focus on "The Gap":** Use `Interaction Donations` to fill the gap between what users do in your UI and what Siri knows.
3.  **Prioritize Transparency:** For actions with side effects (like deleting or updating shared data), correctly implement `EntityOwnershipProviding` to trigger appropriate safety confirmations.
4.  **Use Annotations Sparingly:** Do not over-annotate your UI. Focus on the primary content the user is interacting with.
5.  **Test for Voice-Only:** Always ensure that your `full` and `supporting` strings are descriptive enough to stand alone on devices like AirPods, where visual context is unavailable.
6.  **Maintain the Index:** Keep your semantic index synchronized. Use the `IndexEntityQuery` for automated re-indexing to ensure Siri is always suggesting the most recent user data.

<!-- resources -->

---

## Resources

- [App Intents Testing](https://developer.apple.com/documentation/AppIntentsTesting)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Donations and discovery](https://developer.apple.com/documentation/AppIntents/donations-and-discovery)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/343/4/00190d1d-55b6-4eb2-9ee3-e09f3d8d1c7d/downloads/wwdc2026-343_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/343/4/00190d1d-55b6-4eb2-9ee3-e09f3d8d1c7d/downloads/wwdc2026-343_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/343/
