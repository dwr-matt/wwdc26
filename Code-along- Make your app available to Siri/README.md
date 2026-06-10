# Code-along: Make your app available to Siri

# WWDC 2026 Summary: Code-along: Make your app available to Siri

## Overview
This session provides a hands-on guide for developers to integrate their applications into the Apple Intelligence ecosystem using **App Intents** and **App Schemas**. Using "ComicCal," a SwiftUI calendar app, the presenter demonstrates how to transform a screen-bound application into one that can be fully managed via natural language conversations with Siri. By leveraging standardized app schemas, developers can enable Siri to understand app content, perform actions, and surface custom UI snippets without the need for manual training phrases or complex natural language processing.

## Key Concepts, APIs, and Frameworks
*   **App Intents Framework:** The core framework for exposing app functionality to system features like Siri and Shortcuts.
*   **App Schemas:** Pre-defined structures provided by Apple that describe common app domains (e.g., Calendar). They allow Siri to reason about your app’s entities, parameters, and actions out of the box.
*   **App Entities:** Type-safe representations of your app's data that Siri can understand.
    *   **`IndexedEntity`:** Conforming to this protocol enables semantic search via the Spotlight index, allowing Siri to resolve items by name, property, or context.
    *   **`TransientAppEntity`:** Used for temporary entities that do not require a unique identifier or independent lookup (e.g., attendees inside an event).
*   **`IntentPersonType`:** A system-standard way to represent people with contact info, facilitating interoperability between apps.
*   **`CSSearchableIndex`:** Used to donate and index entities, ensuring that Siri can retrieve and display them.

## Code Patterns and Techniques
*   **Code Snippets:** The presenter emphasizes using Xcode code snippets (e.g., `calendar_event`, `calendar_attendee`) to scaffold the necessary boilerplate for intents and entities.
*   **Dependency Injection:** Using the `@Dependency` property wrapper within Intents and Queries to inject shared resources (like a data manager) rather than re-instantiating them.
*   **On-Screen Awareness:**
    *   **`AppEntityIdentifier` modifier:** Connects a list view to its underlying entities.
    *   **`UserActivity` modifier:** Tells the system which specific entity is currently "front and center" in the UI, enabling conversational context (e.g., "Email the people in *this* event").
*   **Optional Parameter Handling:** In `Update` intents, the `IntentParameter` provides a `value` state. This distinguishes between:
    *   **Unset:** Parameter not part of the request.
    *   **Set with `nil`:** The user explicitly wants to clear the value.
    *   **Set with a value:** The user wants to update the property.
*   **Custom UI Snippets:** By returning a `SnippetView` in an intent’s `perform` method, developers can provide a custom, branded SwiftUI card to display in the Siri interface.

## Practical Takeaways for Developers
1.  **Don't Build from Scratch:** Use App Schemas to map your app’s data. This offloads the burden of Natural Language Understanding (NLU) to the system.
2.  **Indexing is Essential:** Entities must be donated to the system index (via `indexAppEntities`) to be discoverable by Siri and Spotlight.
3.  **Use `Open` Intents:** Implement an "Open" intent to allow Siri to navigate the user directly to a specific record (e.g., a specific event detail view) rather than just launching the app's home screen.
4.  **Adopt Progressive Integration:** Start by defining entities, then move to on-screen awareness, and finally, add action-oriented intents (Create/Update/Delete).
5.  **Test Thoroughly:** The App Intents framework includes robust testing capabilities. Developers should utilize these to ensure Siri behavior is predictable and accurate.
6.  **Keep Snippets Lightweight:** While custom UI in Siri is powerful, keep views performant and visually aligned with the app’s branding.

<!-- resources -->

---

## Resources

- [Integrating your calendar app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-calendar-app-with-apple-intelligence)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Donations and discovery](https://developer.apple.com/documentation/AppIntents/donations-and-discovery)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [Calendar](https://developer.apple.com/documentation/AppIntents/app-schema-domain-calendar)
- [App schema domains](https://developer.apple.com/documentation/AppIntents/app-schema-domains)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/344/4/ee45cb19-e252-41f4-a2e0-e9b59238c7aa/downloads/wwdc2026-344_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/344/4/ee45cb19-e252-41f4-a2e0-e9b59238c7aa/downloads/wwdc2026-344_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/344/
