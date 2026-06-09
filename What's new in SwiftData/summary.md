# What's new in SwiftData

This session from the 2026 WWDC (covering Apple's 2027 SwiftData releases) introduces several advanced features designed to enhance data organization, improve interoperability with third-party frameworks, and streamline reactivity across non-SwiftUI components.

### **Session Overview**
The session focuses on four major advancements in SwiftData: sectioned fetching for organized UI, `Codable` support for non-model types, a new `ResultsObserver` API for observing data outside of SwiftUI views, and a `HistoryObserver` for reacting to persistent store transactions. These updates aim to make SwiftData more flexible for complex applications, including those using SceneKit or custom state controllers.

---

### **Key Concepts & APIs**
*   **Sectioned Queries:** The `@Query` macro now supports a `sectionBy` parameter, allowing developers to group data automatically by a specific model property (e.g., grouping `Trips` by `destination`).
*   **Codable Attributes:** SwiftData can now store types that are not explicitly marked with the `@Model` macro, provided they conform to `Codable`. This acts as an "escape hatch" for types owned by other frameworks (like MapKit's `MKMapItem.ID`).
*   **ResultsObserver:** A new API that brings the reactivity of `@Query` to non-SwiftUI contexts. It monitors the data store for changes and uses Swift Observation to trigger updates in any class or architecture.
*   **HistoryObserver:** Designed to monitor persistent history transactions. It tracks changes made to the store and increments an `eventCounter` whenever new transactions occur, allowing for efficient syncing with external systems (like servers).

---

### **Demonstrated Code Patterns**
*   **Sectioning Implementation:**
    *   Initialize `@Query` with `sectionBy: \.property`.
    *   Access sections via the property wrapper’s underscore name (`_trips.sections`).
    *   Iterate through sections using `ForEach` in SwiftUI, using the section ID as a header and the section contents as the list items.
*   **Codable Storage:**
    *   Simply annotate a property with `@Attribute(.codable)`. 
    *   *Note:* These properties are opaque; they cannot be used in predicates or sort descriptors and will not trigger migrations if the structure changes.
*   **Non-SwiftUI Observation:**
    *   Use `ResultsObserver` in custom classes (e.g., a `MapCameraController`).
    *   Utilize `withContinuousObservation` with the `.didSet` option to trigger logic (like recalculating map bounds) whenever data changes.
    *   Store the returned `ObservationTrackingToken` as a property to ensure observation remains active for the object's lifetime.
*   **Syncing via History:**
    *   Initialize `HistoryObserver` with specific filters for `authors` or `model types`.
    *   Observe the `eventCounter` property within a `withContinuousObservation` block.
    *   When the counter increments, trigger a function to fetch the latest history via the `ModelContext.fetchHistory` API.

---

### **Practical Takeaways for Developers**
1.  **Prefer `@Model` for your own types:** While `Codable` attributes allow you to persist third-party types, they are opaque to the store. Always prefer using native `@Model` types whenever possible to maintain support for filtering, sorting, and indexing.
2.  **Architecture Flexibility:** If you are building non-SwiftUI features (e.g., game engines, complex background sync, or custom controller logic), stop trying to hack `@Query` into those layers. Use `ResultsObserver` for clean, idiomatic reactivity.
3.  **Optimize Syncing:** Do not re-process the entire database when checking for updates. Use `HistoryObserver` to precisely detect when a transaction has occurred and use the `fetchHistory` API to pull only the incremental changes.
4.  **Forward/Backward Compatibility:** Since `Codable` attributes don't participate in standard SwiftData migrations, ensure the serialization logic inside your codable types is robust enough to handle schema changes over time.

<!-- resources -->

---

## Resources

- [SwiftData](https://developer.apple.com/documentation/SwiftData)
- [Adopting SwiftData for a Core Data app](https://developer.apple.com/documentation/CoreData/adopting-swiftdata-for-a-core-data-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/274/4/87fb1efb-9956-414e-8c99-f2579fe86da2/downloads/wwdc2026-274_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/274/4/87fb1efb-9956-414e-8c99-f2579fe86da2/downloads/wwdc2026-274_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/274/
