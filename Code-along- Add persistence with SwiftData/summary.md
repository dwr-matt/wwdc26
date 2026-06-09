# Code-along: Add persistence with SwiftData

# Session Summary: Code-along: Add persistence with SwiftData

## Overview
This WWDC 2026 session provides a practical guide for transitioning an existing SwiftUI application (the "Wishlist" sample app) from an in-memory, volatile data source to a robust, persistent storage layer using **SwiftData**. Matthew Turk demonstrates the end-to-end process of refactoring data models, defining database schemas, implementing efficient queries, and integrating these models into the SwiftUI view layer to achieve real-time, persistent data flow.

## Key Concepts, APIs, and Frameworks
*   **SwiftData:** Apple’s declarative persistence framework used to replace manual in-memory state management.
*   **`@Model` Macro:** Replaces `Observable` to transform standard Swift classes into persistent database entities.
*   **`@Query` Macro:** The primary mechanism for fetching data from the `ModelContext`. It automatically triggers view updates when the underlying database changes.
*   **ModelContext:** The environment-level coordinator that handles the saving, fetching, and tracking of model objects.
*   **Model Inheritance:** A technique for sharing common properties among related data types (e.g., `TripGoal` and `ActivityGoal` inheriting from `Goal`), allowing for polymorphic data structures.
*   **Persistent External Reference:** A method for handling large binary data (like high-resolution images) by storing them externally while keeping the database performant by caching small thumbnails directly in the schema.

## Code Patterns & Techniques
*   **Refactoring Models:** Replacing `let` constants with `var` variables to allow SwiftData to populate them at runtime, and ensuring properties conform to `Codable` for serialization.
*   **Relationship Management:** Using `@Relationship` to define connections (e.g., one-to-many from `Trip` to `Activity`). This enables cascading deletes, where removing a parent object automatically cleans up associated child models.
*   **Optimizing Performance:** 
    *   Moving from fetching "all items and filtering in memory" to using **Predicates** in the `@Query` macro to fetch only the necessary data directly from the disk.
    *   Implementing a "thumb-caching" pattern to prevent loading high-resolution assets until necessary.
*   **Continuous Observation:** Using the `withContinuousObservation` function (introduced in 2027) to trigger side effects—such as updating a `dateEdited` timestamp or recalculating completion status—whenever a model property changes, ensuring the UI stays perfectly synced.
*   **Error Handling:** Wrapping data operations in state-driven error handling, passing failures to telemetry systems, and surfacing user-facing alerts when persistence operations fail.

## Practical Takeaways for Developers
*   **Drastic Code Reduction:** Migrating to SwiftData often allows for the removal of custom "Data Source" classes, manual sorting/filtering logic, and complex state management boilerplate.
*   **Think in Predicates:** When designing for performance, avoid loading large datasets into RAM. Use predicates in your queries to let the database handle filtering, which significantly reduces the app's memory footprint.
*   **Model Inheritance:** Utilize class inheritance for models when you have a common base set of properties but need specialized behavior for different types of data, keeping your schema clean and modular.
*   **Automatic Lifecycle:** SwiftData handles the persistence cycle. By using the `@ModelContainer` scene modifier, the framework manages object graph lifecycles, saving edits automatically and updating views in response to changes without manual state updates.
*   **UI Integration:** Replace environment-injected data sources directly with `@Query` properties. This simplifies view initializers and makes the relationship between the persistent store and the UI components transparent.

<!-- resources -->

---

## Resources

- [Wishlist: Planning travel in a SwiftUI app](https://developer.apple.com/documentation/SwiftUI/wishlist-planning-travel-in-a-swiftui-app)
- [SwiftData](https://developer.apple.com/documentation/SwiftData)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/275/4/7c64f887-3c3c-4bdf-8472-72d6b96f8e3d/downloads/wwdc2026-275_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/275/4/7c64f887-3c3c-4bdf-8472-72d6b96f8e3d/downloads/wwdc2026-275_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/275/
