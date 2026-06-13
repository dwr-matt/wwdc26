# What's New in SwiftData — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/swiftdata/resultsobserver.json
- https://developer.apple.com/tutorials/data/documentation/swiftdata/historyobserver.json

---

## Four New Features

1. **Sectioned Query** — group `@Query` results by a property
2. **Codable Attribute** — persist third-party types via `@Attribute(.codable)`
3. **ResultsObserver** — `@Query`-like reactivity outside SwiftUI views
4. **HistoryObserver** — react to persistent history transactions (for sync)

---

## 1. Sectioned Query

`@Query` gains a `sectionBy` parameter:

```swift
@Query(sectionBy: \.destination, sort: \.startDate)
var trips: [Trip]
```

Access sections via the property wrapper's underscore-prefixed name:

```swift
List {
    ForEach(_trips.sections) { section in
        Section(header: Text(section.id)) {
            ForEach(section) { trip in
                TripRowView(trip: trip)
            }
        }
    }
}
```

- `section.id` — the value of the `sectionBy` key path for all items in that section (e.g. `"Tokyo"`)
- `_trips` (underscore prefix) is the property wrapper itself; `.sections` returns the grouped results

---

## 2. Codable Attribute

**Problem:** Third-party classes (e.g. `MKMapItem.Identifier`) can't be inspected by SwiftData — causes a fatal error at launch.

**Fix:** Mark with `@Attribute(.codable)` to delegate serialization to the type's own `Codable` implementation:

```swift
@Model
class Trip {
    var name: String
    var destination: String
    @Attribute(.codable) var mapItemID: MKMapItem.Identifier?
}
```

**Constraints (important):**
- Codable attributes are **opaque** to SwiftData — cannot be used in `Predicate` (filtering) or `SortDescriptor` (sorting)
- Shape changes to the codable type (add/remove properties) **do not trigger migration** — you must maintain forward/backward compatibility yourself
- This is an **escape hatch** for types you don't own. For your own types, always use `@Model` or supported value types to retain filtering, sorting, and indexing

---

## 3. ResultsObserver — Reactive Queries Outside SwiftUI

`@Query` only works inside SwiftUI views. `ResultsObserver` brings the same reactivity to any class using Swift Observation.

```swift
// From docs — verified
final class ResultsObserver<Element, SectionName>
    where Element: PersistentModel, SectionName: Hashable
```

**Key initializers:**

```swift
// Unsectioned
convenience init(
    filterBy: Predicate<Element>?,
    sortBy: [SortDescriptor<Element>]?,
    modelContext: ModelContext,
    isolation: isolated (any Actor)?
) throws

// Sectioned
convenience init(
    filterBy: Predicate<Element>?,
    sortBy: [SortDescriptor<Element>]?,
    sectionBy: KeyPath<Element, String>,
    modelContext: ModelContext,
    isolation: isolated (any Actor)?
) throws
```

**Key properties:**

```swift
var results: FetchResultsCollection<Element> { get }
var sections: ResultsSectionCollection<Element, SectionName>? { get }
```

**Usage pattern (MapCameraController from transcript):**

```swift
@Observable
class MapCameraController {
    var cameraBounds: MapCameraBounds = .default
    private var observerToken: ObservationTrackingToken?

    init(modelContext: ModelContext) throws {
        let observer = try ResultsObserver<Trip, Never>(
            filterBy: nil,
            sortBy: nil,
            modelContext: modelContext
        )
        observerToken = withContinuousObservation(of: observer.results, options: .didSet) {
            self.cameraBounds = calculateBounds(for: observer.results)
        }
    }
}
```

- `withContinuousObservation` + `.didSet` — fires callback on every change
- Store the returned `ObservationTrackingToken` as a property — it controls the observation lifetime; observation stops when the token is released
- Responds to local changes, remote changes from other contexts, and CloudKit sync

---

## 4. HistoryObserver — React to Store Transactions

`ResultsObserver` answers "what data exists now." `HistoryObserver` answers "what just changed" — useful for syncing to an external server or processing writes from an App Extension.

```swift
// From docs — verified
final class HistoryObserver

convenience init(
    historyTokens: [String: any HistoryToken]?,
    observedModels: [any PersistentModel.Type],
    authors: Set<String>,
    modelContainer: ModelContainer,
    isolation: isolated (any Actor)?
) throws

var eventCounter: Int  // increments each time new transactions are available
```

**Usage pattern (server sync from transcript):**

```swift
@Observable
class ServerSyncController {
    private var observerToken: ObservationTrackingToken?

    init(modelContainer: ModelContainer) throws {
        let historyObserver = try HistoryObserver(
            observedModels: [Trip.self],
            authors: ["app"],          // exclude changes that came FROM the server
            modelContainer: modelContainer
        )
        observerToken = withContinuousObservation(of: historyObserver.eventCounter, options: .didSet) {
            _ = historyObserver.eventCounter   // access it so Swift Observation tracks it
            Task { await self.processChanges() }
        }
    }

    func processChanges() async {
        // modelContext.fetchHistory(...) → upload incremental changes to server
    }
}
```

- `eventCounter` — the single observable property; increment means new transactions are waiting
- `authors` filter — pass `["app"]` to see only what your app wrote (not server-synced data flowing back)
- `observedModels` filter — scope to specific model types
- `historyTokens` — tracks position in history for incremental fetches; pass back on next init to resume where you left off

---

## Decision Guide

| Scenario | API |
|---|---|
| SwiftUI view, display a list | `@Query` |
| SwiftUI view, grouped list | `@Query(sectionBy:)` + `_results.sections` |
| Non-SwiftUI class needs live data (map, SceneKit) | `ResultsObserver` |
| Need to know what changed (server sync, App Extension) | `HistoryObserver` |
| Storing a third-party class that's `Codable` | `@Attribute(.codable)` |

---

## Related Sessions
- [Code-along: Add persistence with SwiftData](../Code-along-%20Add%20persistence%20with%20SwiftData/) — step-by-step adoption
- *Track model changes with SwiftData history* (WWDC 2024) — persistent history fundamentals

---

## Resources
- [SwiftData](https://developer.apple.com/documentation/SwiftData)
- [Adopting SwiftData for a Core Data app](https://developer.apple.com/documentation/CoreData/adopting-swiftdata-for-a-core-data-app)
- Session page: https://developer.apple.com/videos/play/wwdc2026/274/
