# Build intelligent Siri experiences with App Schemas

# WWDC 2026: Build intelligent Siri experiences with App Schemas

## Session Overview
This session explores how developers can leverage Apple Intelligence to create deeper, more contextual Siri integrations. The core message is that **App Intents** are the foundation for system-wide intelligence. By modeling app content as **App Entities** and adopting **App Schemas**, developers enable Siri to understand, search, and perform actions within their apps—even across cross-app workflows. The session emphasizes moving beyond simple command-and-control triggers to building natural, context-aware experiences.

---

## Key Concepts, APIs, and Frameworks

*   **App Intents Framework:** The central framework for exposing app functionality to Siri and the system.
*   **App Entities:** Structured representations of the data within your app (e.g., a "Message," "Contact," or "Photo"). They define what an item is, how it is identified, and which properties are relevant.
*   **App Schemas:** Predefined contracts for common domains (e.g., Mail, Messages, Photos). Conforming your entities and intents to these schemas allows Siri to reason about them without custom training.
*   **Semantic Indexing:** By adopting the `IndexedEntity` protocol, developers allow Siri to perform semantic searches rather than simple string matching, enabling complex queries like "Show messages from Flare about movies."
*   **Entity String Query:** A fallback for when indexing is not feasible (e.g., large data sets or server-side content), giving developers full control over how to match user input to entities.
*   **Transferable:** An API that allows entities to be passed between different apps, enabling workflows like "Email my wife this reply from Bubbles."
*   **On-Screen Awareness:** Uses `UserActivity` or view annotations to allow Siri to understand what the user is currently viewing, enabling context-dependent commands like "Summarize this."

---

## Code Patterns & Techniques

*   **Schema Adoption:** Developers map their app’s existing logic to system-defined schemas. Xcode provides **"Fix-its" and design hints**; if you adopt a primary action schema (like `SendMessage`), Xcode will trigger build errors if mandatory related schemas (like `DraftMessage`) are missing, providing generated stubs to ensure a complete experience.
*   **Entity Linking:** Annotating SwiftUI views with entities connects the UI directly to the data model, allowing Siri to "see" what is on-screen.
*   **Content Transfer Patterns:**
    *   **`IntentValueQuery`:** Used when incoming cross-app content should resolve to an existing local entity.
    *   **Importing:** Used when incoming content should result in the creation of a brand-new entity within the app.
*   **Testing Infrastructure:** 
    *   **App Intents Testing:** A framework for unit-testing business logic in isolation (without launching Siri or the host app).
    *   **Shortcuts/Spotlight/Siri:** A tiered validation approach: test logic in isolation, intent configuration in the Shortcuts app, indexing in Spotlight, and finally the full natural language flow in Siri.

---

## Practical Takeaways for Developers

1.  **Start with Entities:** Before focusing on actions, define your app’s data as `AppEntities`. A well-defined entity is the prerequisite for all subsequent intelligence.
2.  **Use Schemas, Not Custom Intents:** Whenever possible, adopt standard App Schema domains. This ensures Siri can guide the user through missing parameters and handle natural language parsing automatically.
3.  **Leverage Xcode’s Design Guidance:** Pay attention to compiler errors when implementing schemas. They are designed to ensure you don't miss critical UX steps (like confirmation or drafting) required by the system.
4.  **Prioritize Semantic Search:** If your content can be indexed locally, use `IndexedEntity`. This provides a significant quality boost to Siri's ability to answer natural language questions about your app’s data.
5.  **Build for Continuity:** Use `Transferable` and view annotations to make your app a "good citizen" in the Apple ecosystem. Allowing your content to flow into other apps creates powerful, multi-step productivity workflows for users.
6.  **Test Early:** Use the new testing framework to validate your intents before you ever attempt to invoke them via voice, saving significant time during the development lifecycle.

<!-- resources -->

---

## Resources

- [Integrating your messaging app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-messaging-app-with-apple-intelligence)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [Messages](https://developer.apple.com/documentation/AppIntents/app-schema-domain-messages)
- [App schema domains](https://developer.apple.com/documentation/AppIntents/app-schema-domains)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/240/4/d46aac11-3990-42cd-bb33-4ce5e958b902/downloads/wwdc2026-240_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/240/4/d46aac11-3990-42cd-bb33-4ce5e958b902/downloads/wwdc2026-240_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/240/
