# Discover new capabilities in the App Intents framework — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/appintents/adopting-app-intents-to-support-system-experiences.json

---

## Three Areas of New Capabilities

1. **Entity enhancements** — share across apps, surface when relevant, process at scale
2. **Native type support** — Duration, PersonNameComponents, @UnionValue
3. **Execution control** — LongRunningIntent, CancellableIntent, Execution Targets

---

## 1. Entity Enhancements

### ValueRepresentation — Share Structured Data Across Apps

Existing `DataRepresentation`/`FileRepresentation` work for known formats (PDF, images). For structured types without a file format (e.g., a location that Maps needs), use `ValueRepresentation`:

```swift
extension LandmarkEntity: Transferable {
    public static var transferRepresentation: some TransferRepresentation {
        // ...existing representations...
        ValueRepresentation(exporting: \.placeDescriptor)
    }
}

public var placeDescriptor: PlaceDescriptor {
    PlaceDescriptor(
        representations: [.coordinate(landmark.locationCoordinate)],
        commonName: landmark.name
    )
}
```

If the entity already has a matching property, a key path is sufficient — no closure needed.

---

### Relevant Entities — Proactively Surface Content

Three content discovery mechanisms and when to use each:

| Mechanism | Best for |
|---|---|
| Spotlight indexing | Searchability and Siri retrieval |
| Interaction donation | Teaching usage patterns for personalized repetition |
| **Relevant Entities (new)** | Surfacing content at specific moments, even if never used before |

Use case: a brand new running playlist that no one has played yet and hasn't been searched — register it as relevant when a workout starts.

API pattern (from transcript — no verified code in docs):
- Identify the relevant entities
- Create context describing when they're relevant
- Call `updateEntities()` to register
- Entities stay registered until removed

Removal options:
- Remove all entities for a specific context
- Remove specific entities from a context
- Clear all entities across all contexts

---

### EntityCollection — Performance for Bulk Operations

**Problem**: Before an intent runs, the system resolves every entity by calling EntityQuery to populate all its properties — even if `perform()` only needs the ID. For 1000 photos this causes significant latency.

**Fix**: Replace `Array<PhotoEntity>` with `EntityCollection<PhotoEntity>`. The system passes only identifiers — no full resolution.

```swift
public struct TagPhotosIntent: AppIntent {
    @Parameter(title: "Photos", requestValueDialog: "Which photo?")
    var photos: EntityCollection<PhotoEntity>  // was: [PhotoEntity]

    @Parameter(title: "Tag", default: "favorite")
    var tag: String

    public func perform() async throws -> some IntentResult & ProvidesDialog {
        await tagPhotos(with: tag)
        return .result(dialog: "Tagged \(photos.count) photos with '\(tag)'")
    }

    private func tagPhotos(with tag: String) async {
        // Use .identifiers directly — no entity resolution needed
        await modelData.tagPhotos(ids: photos.identifiers, tag: tag)
    }
}
```

Result: tagging 1000 photos went from several seconds to nearly instant.

---

### SyncableEntity — Stable Identity Across Devices

iOS 27 supports cross-device Siri conversations. If entity IDs are generated locally (CoreData OIDs, etc.), the same entity has different IDs on different devices — Siri can't find it.

**If the entity already uses a stable ID** (server UUID, CloudKit record ID):

```swift
@AppEntity(schema: .photos.asset)
struct PhotoEntity: IndexedEntity, SyncableEntity {
    var id: Int  // same across all devices
}
```

**If using local IDs** (e.g., CoreData OIDs), pair them with a stable ID:

```swift
struct PhotoEntity: AppEntity, SyncableEntity {
    var id: SyncableEntityIdentifier<String, String>

    init(localID: String, stableID: String) {
        self.id = SyncableEntityIdentifier(local: localID, stable: stableID)
    }
}
// On-device: system uses local ID
// Cross-device: system uses stable ID
```

---

## 2. Native Type Support

New native parameter types with built-in pickers in Siri, Shortcuts, and Widgets:
- `Duration` — no more custom time pickers
- `PersonNameComponents` — structured name input instead of plain String

### @UnionValue — One Parameter, Multiple Types

```swift
@UnionValue
enum TravelGalleryContent {
    case landmarkCollection(LandmarkCollectionEntity)
    case photoAlbum(PhotoAlbumEntity)

    static var typeDisplayRepresentation: TypeDisplayRepresentation { "Travel Gallery" }
    static let caseDisplayRepresentations: [Cases: DisplayRepresentation] = [
        .landmarkCollection: "Landmark Collection",
        .photoAlbum: "Photo Album",
    ]
}

struct TravelGalleryWidgetIntent: WidgetConfigurationIntent {
    @Parameter var content: TravelGalleryContent?

    static var parameterSummary: some ParameterSummary {
        Switch(\.$content) {
            Case(.landmarkCollection) {
                Summary("Show landmarks from \(\.$content)")
            }
            Case(.photoAlbum) {
                Summary("Cycle through \(\.$content) photos")
            }
            DefaultCase {
                Summary("Show \(\.$content)")
            }
        }
    }
}
```

`@UnionValue` macro generates: type info, case metadata, and picker UI. Works in widgets, Shortcuts, and Siri intent parameters.

---

## 3. Execution Control

### LongRunningIntent — Beyond the 30-Second Limit

```swift
public struct UploadPhotoIntent: LongRunningIntent, CancellableIntent {
    @Parameter(requestValueDialog: "Which photo?")
    public var photo: IntentFile

    public func perform() async throws -> some IntentResult & ProvidesDialog {
        let result = try await performBackgroundTask { @Sendable in
            let chunks = calculateChunks()
            progress.totalUnitCount = Int64(chunks)
            progress.localizedDescription = "\(name)"

            for chunk in 1...chunks {
                try await uploadChunk(chunk)
                progress.completedUnitCount = Int64(chunk)
                progress.localizedAdditionalDescription =
                    "Uploaded \(Int((Double(chunk) / Double(chunks)) * 100))%"
            }
            return "Uploaded \(name) successfully!"
        } onCancel: { reason in
            cleanup(for: reason)
        }
        return .result(dialog: "\(result)")
    }
}
```

Key points:
- `performBackgroundTask { }` — extends execution window beyond 30 seconds
- `progress` — built-in `Progress` object (inherited from `ProgressReportingIntent`)
- System automatically shows progress as a **Live Activity** with a stop button
- Supports background GPU access (needs entitlement) for photo processing or on-device inference

### CancellableIntent — Graceful Cleanup

`onCancel` in `performBackgroundTask` receives a `reason` (user tapped stop, system timeout, resource reclaim). Use it to clean up partial uploads or cancel in-flight requests.

### Execution Targets — Control Which Process Runs

When intent code lives in a shared package used by both main app and extensions, the system picks a process via heuristics. Override when needed (e.g., widget has read-only data access, write intents must run in main app):

```swift
struct UpdateFavoriteIntent: AppIntent {
    static var allowedExecutionTargets: ExecutionTargets { .main }
    // ...
}
```

Available targets: `.main`, `.appIntentsExtension`, `.widgetKitExtension`, or any combination.

---

## Decision Guide

| Scenario | API |
|---|---|
| Share a location/structured data to another app | `ValueRepresentation` |
| Surface new content before anyone has used it | Relevant Entities |
| Intent processes hundreds/thousands of entities | `EntityCollection` |
| App runs on multiple devices, Siri conversations span devices | `SyncableEntity` |
| Parameter needs to accept a Landmark OR a PhotoAlbum | `@UnionValue` |
| Intent takes more than 30 seconds | `LongRunningIntent` |
| Need cleanup when intent is stopped | `CancellableIntent` |
| Widget and main app share code, data contention risk | `allowedExecutionTargets` |

---

## Related Sessions
- [Code-along: Make your app available to Siri](../Code-along-%20Make%20your%20app%20available%20to%20Siri/) — step-by-step integration
- *Explore advanced App Intents features for Siri and Apple Intelligence*
- *Validate your App Intents adoption with App Intents testing*

---

## Resources
- [Adopting App Intents to support system experiences](https://developer.apple.com/documentation/AppIntents/adopting-app-intents-to-support-system-experiences)
- [App Intents](https://developer.apple.com/documentation/AppIntents)
- Session page: https://developer.apple.com/videos/play/wwdc2026/345/
