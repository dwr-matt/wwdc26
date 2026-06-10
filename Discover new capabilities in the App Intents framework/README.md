# Discover new capabilities in the App Intents framework

# Session Summary: Discover New Capabilities in the App Intents Framework (WWDC 2026)

## Overview
This session introduces significant enhancements to the App Intents framework, aimed at providing developers with more control, flexibility, and performance. As a core pillar of Apple Intelligence, the framework has been updated to facilitate deeper integration across the system, including Siri, Shortcuts, Spotlight, and Widgets. The 2027 releases focus on improving how entities travel across apps, how content is surfaced via contextual relevance, and how long-running tasks are handled, all while optimizing performance and process execution.

---

## Key Concepts, APIs, and Frameworks

### 1. Enhanced Entity Interactions
*   **Value Representation:** A new way to share structured data between apps that lack a standard file format (e.g., sharing a location to Maps). By conforming to `ValueRepresentation`, developers can export metadata—like a `PlaceDescriptor`—so the system can act upon it natively.
*   **Relevant Entities:** A new system to proactively suggest content based on context. By registering entities with specific relevance criteria (e.g., "suggest this playlist when a workout starts"), the system can surface content even if it hasn't been searched or interacted with previously.
*   **Syncable Entity:** Allows entities to maintain a stable identity across different devices, solving issues where local device IDs cause failures in multi-device Siri conversations.

### 2. Efficiency and Performance
*   **Entity Collection:** Optimizes intents that handle large numbers of entities. Instead of resolving every entity into a full object before execution (which is resource-intensive), `EntityCollection` passes only the identifiers, significantly reducing overhead for bulk operations.

### 3. Execution and Process Control
*   **Long-Running Intents:** Extends the 30-second execution limit for intents. It leverages background task management and automatically generates a **Live Activity** to track progress and allow users to stop the process.
*   **Cancelable Intent:** Provides a hook (`uncancel`) to gracefully clean up partial data or cancel in-flight requests when an intent is stopped.
*   **Execution Targets:** Allows developers to explicitly define which process (Main App, Widget Extension, or App Intent Extension) should execute an intent, overriding system heuristics to prevent data contention (e.g., forcing a write operation to the main app).

### 4. Native Type Support
The framework now includes native support for common data types, including:
*   **Duration:** Eliminates the need for custom pickers.
*   **PersonNameComponents:** Provides structured name inputs rather than plain strings.
*   **Union Values:** Uses a Swift macro to allow a single parameter to accept multiple distinct types (e.g., a widget parameter that accepts either a `Landmark` or a `PhotoAlbum`).

---

## Code Patterns & Techniques
*   **Concise Property Mapping:** When using `ValueRepresentation`, developers can use key paths to directly map entity properties to system descriptors, drastically reducing boilerplate code.
*   **Union Value Macros:** By marking an enum with the `@UnionValue` macro, the system automatically handles the mapping of case metadata and provides a pre-built picker UI.
*   **Background Lifecycle:** When using `LongRunningIntent`, wrap logic in `performBackgroundTask` to safely extend the execution window, and utilize the provided `Progress` object for system-level status updates.
*   **Stable ID Pairing:** For entities using local identifiers (like CoreData OIDs), use `SyncableEntityIdentifier` to pair the local ID with a stable, server-assigned identifier to ensure consistency across the ecosystem.

---

## Practical Takeaways for Developers
*   **Choose the right discovery mechanism:**
    *   **Spotlight:** Best for searchability and retrieval by Siri.
    *   **Interaction Donation:** Best for teaching the system usage patterns for personalized repetition.
    *   **Relevant Entities:** Best for surfacing content in specific, context-sensitive moments.
*   **Performance Optimization:** If your intent processes large lists (e.g., tagging hundreds of photos), immediately transition from `Array<Entity>` to `EntityCollection` to prevent UI hangs and timeout errors.
*   **Process Architecture:** Evaluate your intent placement; if you share data between a widget and the main app, use **Execution Targets** to route write-heavy intents to the main app and read-only intents to the extension to maintain data integrity.
*   **Future-Proofing:** Always add `SyncableEntity` to your entity definitions if your app supports multi-device usage to ensure a seamless Siri experience for users across their device fleet.

<!-- resources -->

---

## Resources

- [Adopting App Intents to support system experiences](https://developer.apple.com/documentation/AppIntents/adopting-app-intents-to-support-system-experiences)
- [App Intents](https://developer.apple.com/documentation/AppIntents)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/345/4/bc719e14-772a-4737-aceb-6e54cda6b511/downloads/wwdc2026-345_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/345/4/bc719e14-772a-4737-aceb-6e54cda6b511/downloads/wwdc2026-345_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/345/
