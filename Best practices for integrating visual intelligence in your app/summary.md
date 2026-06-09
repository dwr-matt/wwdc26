# Best practices for integrating visual intelligence in your app

# WWDC 2026: Best Practices for Integrating Visual Intelligence

## Session Overview
This session introduces developers to the expanded capabilities of **Visual Intelligence**, which now extends beyond iOS to include iPadOS and macOS. The session focuses on two primary integration strategies: **Image Search**, where apps provide context-aware results based on user-captured imagery, and **System Store Integrations**, which allow apps to react to data (such as calendar events, contacts, or health metrics) added by the system via Visual Intelligence. By building a sample music discovery app, the presenter demonstrates how to create a seamless bridge between visual input and app functionality.

## Key Concepts, APIs, and Frameworks
*   **App Intents:** The core framework used to define entities and perform actions.
*   **Visual Intelligence Framework:** The primary API for interacting with system-level image search.
*   **Vision Framework:** Used for on-device machine learning tasks, such as generating feature prints for image similarity matching.
*   **System Stores:** Using `EventKit`, `Contacts`, and `HealthKit` to retrieve data that users have "scanned" or "logged" using Visual Intelligence.
*   **Semantic Content Descriptor:** An object containing the pixel buffer and metadata captured from the camera or screen, passed to your app to trigger a search.

## Code Patterns & Techniques
### 1. Defining Searchable Content
Developers use `AppEntity` to define nouns within their app (e.g., an Album). 
*   **Display Representation:** Crucial for UI; you are limited to ~3 lines of text and a thumbnail. It is recommended to use small thumbnail URLs rather than full-resolution assets to keep the result sheet fast and performant.

### 2. Implementing `IntentValueQuery`
This protocol allows the system to query your app for matching content. 
*   **Pattern:** The query accepts a `semanticContentDescriptor`.
*   **Technique:** Inside the query, convert the pixel buffer to a `CGImage` and use the Vision Framework to compute a **feature print**. Comparing this against pre-computed feature prints in your catalog allows for fast, on-device similarity searching.

### 3. Handling Navigation with Open Intents
When a user taps a result, your app must navigate to the specific item.
*   **Technique:** Reuse existing `OpenIntent` implementations if your app already supports App Intents. Keep the `perform` method lightweight—save heavy data loading for after the view has appeared.

### 4. Advanced Search & UI Flow
*   **Union Types:** If your app provides multiple result types (e.g., Albums and Concerts), use a `UnionValue` enum to group them.
*   **Semantic Content Search Schema:** Implement this to provide a "More Results" button. This allows the system to launch your app and deep-link directly into your native search UI, pre-populated with the captured context.

## Practical Takeaways for Developers
*   **Platform Parity:** Most logic is shared across iOS, iPadOS, and macOS. However, remember that **mobile** inputs are often physical objects (camera), while **desktop/tablet** inputs are often digital screenshots. Test both.
*   **Performance Optimization:** 
    *   Pre-compute expensive data (like feature prints) for your catalog. 
    *   Rank results by similarity so the most relevant items appear first.
    *   Return an empty array if no good matches are found; the system handles the "no results" state.
*   **System Integration:** You don't always need to build a custom bridge. By adopting standard frameworks like `EventKit` or `HealthKit`, your app will automatically receive data that users log via Visual Intelligence without needing extra custom code.
*   **User Experience:** If an item is tapped, ensure the user lands exactly on that content. Use these integrations to reduce friction by letting the "real world" context drive in-app discovery.

<!-- resources -->

---

## Resources

- [Integrating your app with visual intelligence](https://developer.apple.com/documentation/VisualIntelligence/integrating-your-app-with-visual-intelligence)
- [Visual Intelligence](https://developer.apple.com/documentation/VisualIntelligence)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/297/5/25343020-b502-4808-967a-6f6460789dc2/downloads/wwdc2026-297_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/297/5/25343020-b502-4808-967a-6f6460789dc2/downloads/wwdc2026-297_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/297/
