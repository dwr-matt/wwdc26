# Build intelligent Siri experiences with App Schemas — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/appintents/integrating-your-messaging-app-with-apple-intelligence.json
- https://developer.apple.com/tutorials/data/documentation/appintents/making-app-entities-available-in-spotlight.json
- https://developer.apple.com/tutorials/data/documentation/appintents/providing-contextual-cues-to-apple-intelligence-and-siri.json

---

## Architecture Overview

```
AppEntity (data layer)  → adopt AppSchema → Siri knows what this thing is
AppIntent (action layer) → adopt AppSchema → Siri knows what can be done
App Schema Domain        → a complete set of schema contracts (e.g., Messages domain)
```

App Entities are not a new data model — they describe existing app content so the system can understand it.

---

## AppEntity with Schema Conformance

```swift
@AppEntity(schema: .messages.message)
struct MessageEntity: IndexedEntity {
    static let defaultQuery = MessageQuery()

    var id: UUID
    var author: ContactEntity
    var body: AttributedString?
    var conversation: ConversationEntity
    var date: Date
    var attachments: [IntentFile]

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(
            title: "\(body ?? "")",
            subtitle: "\(conversation.displayName)"
        )
    }
}
```

The `schema:` parameter tells Siri what category of thing this is. Siri already knows how to reason about it — no custom training needed.

---

## EntityQuery

```swift
extension MessageEntity {
    struct MessageQuery: EntityQuery {
        @Dependency var model: ModelManager

        func entities(for identifiers: [MessageEntity.ID]) async throws -> [MessageEntity] {
            try await model.messageEntities(for: identifiers)
        }

        func suggestedEntities() async throws -> [MessageEntity] {
            try await model.fetchRecentMessageEntities(limit: 5)
        }
    }
}
```

---

## Entity Resolution: Two Approaches

### IndexedEntity (recommended — enables semantic search)

Entity enters the system's semantic index. Supports concept queries like "messages from Flare about movies" — not string matching.

```swift
struct LandmarkEntity: IndexedEntity {
    // Map description to Spotlight's contentDescription key
    @ComputedProperty(indexingKey: \.contentDescription)
    var description: String { landmark.description }

    // Custom Spotlight key
    @ComputedProperty(
        customIndexingKey: CSCustomAttributeKey(
            keyName: "com_MyApp_LandmarkEntity_continent"
        )!
    )
    var continent: String { landmark.continent }
}

// Index on launch
try await CSSearchableIndex.default().indexAppEntities(messageEntities)
```

Implement `IndexedEntityQuery` to handle Spotlight reindex requests:

```swift
struct MessageQuery: IndexedEntityQuery {
    func reindexAllEntities(indexDescription: CSSearchableIndexDescription) async throws {
        let all = try await store.fetchAll()
        try await CSSearchableIndex(name: "MyApp").indexAppEntities(all)
    }
}
```

### EntityStringQuery (fallback — full manual control)

Use when data is large, server-side, or changes too frequently to index. Siri hands you the user's raw input string; you find and return matching entities. Less natural language quality but full control.

---

## AppIntent with Schema

```swift
@AppIntent(schema: .messages.sendMessage)
struct SendMessageIntent: AppIntent {
    var destination: MessageDestination
    var content: AttributedString?
    @Parameter(supportedContentTypes: [.image]) var attachments: [IntentFile]

    @Dependency var model: ModelManager

    func perform() async throws -> some ReturnsValue<[MessageEntity]> {
        let ids = try await model.sendMessage(
            toRecipientIDs: destination.persons.map(\.id),
            messageText: String(content?.characters ?? "")
        )
        return .result(value: try await model.messageEntities(for: ids))
    }
}
```

### Schema Domain Completeness: Xcode Build Errors as Design Hints

Adopting `sendMessage` triggers a build error if `draftMessage` is missing — Siri needs drafting for confirmation flows. This is intentional design guidance, not just a compiler error. Click into it and Xcode generates a stub adoption.

---

## On-Screen Awareness

Connects visible UI to structured entity data so Siri understands "this message" or "that contact" without the user naming them explicitly.

**When to use which API (from transcript):**
- `UserActivity` — one primary thing on screen (e.g., composing a message, viewing a document)
- View annotations — multiple meaningful items visible at once (e.g., message list rows)

### Simple view annotation (list rows)

```swift
// SwiftUI
view.appEntityIdentifier(EntityIdentifier(for: MessageEntity.self, identifier: message.id))

// UIKit / AppKit
view.appEntityIdentifier = EntityIdentifier(for: NoteEntity.self, identifier: note.id)
```

### UserActivity (one primary entity or legacy OS support)

```swift
// SwiftUI
view.userActivity("com.example.viewNote") { activity in
    activity.appEntityIdentifier = EntityIdentifier(for: NoteEntity.self, identifier: note.id)
}

// UIKit
let activity = NSUserActivity(activityType: "com.example.viewNote")
activity.appEntityIdentifier = EntityIdentifier(for: NoteEntity.self, identifier: note.id)
responder.userActivity = activity
```

### Custom layout (Canvas, freeform positioning)

```swift
Canvas { ... }
.appEntityUIElements { context in
    stickyNotes.compactMap { note in
        AppEntityUIElement(
            identifier: EntityIdentifier(for: StickyNote.self, identifier: note.id),
            bounds: note.frame,
            state: State(isSelected: note.isSelected)
        )
    }
}
```

UIKit equivalent uses `view.appEntityUIElementProvider = { view, context in ... }`.

---

## Content Transfer (Cross-App Workflows)

### Export: let other apps receive your entity

```swift
struct ContactEntity: AppEntity, Transferable {
    static var transferRepresentation: some TransferRepresentation {
        IntentValueRepresentation(
            exporting: { contact in
                IntentPerson(
                    identifier: .applicationDefined(contact.id),
                    name: .displayName(contact.name),
                    handle: .init(emailAddress: contact.email)
                )
            },
            importing: { person in
                guard case let .applicationDefined(id) = person.identifier?.value else {
                    throw ConversionError.missingData
                }
                return ContactEntity(id: id, name: person.name.displayString, email: ...)
            }
        )
    }
}
```

### Receiving incoming content (two cases)

| Incoming content | Approach |
|---|---|
| Refers to something that already exists | `IntentValueQuery` — resolve to existing entity |
| Brand new content | `importing` in `IntentValueRepresentation` — create a new entity |

Many apps use both depending on the intent and workflow.

---

## Notification Entity Annotation

```swift
let content = UNMutableNotificationContent()
content.appEntityIdentifiers = [
    EntityIdentifier(for: MessageEntity.self, identifier: message.id)
]
```

---

## Intent Donation

Donate after the user completes an action so Siri learns usage patterns and can proactively suggest:

```swift
try await IntentDonationManager.shared.donate(
    intent: intent,
    result: .result(value: messages.map(\.entity))
)
```

---

## Testing Strategy (from transcript)

| Stage | Tool | Tests |
|---|---|---|
| 1 | App Intents Testing | Business logic in isolation — no Siri, no host app |
| 2 | Shortcuts app | Intent parameter configuration, UI |
| 3 | Spotlight | Indexing correctness |
| 4 | Siri | Full natural language flow |

---

## Key Principles

1. **Start with entities, then intents** — a well-defined entity is the prerequisite for everything else
2. **Use schema domains, not custom intents** — Siri already knows how to handle standard schemas
3. **Trust Xcode build errors** — they're design hints telling you which dependent schemas are missing
4. **Prefer IndexedEntity over EntityStringQuery** — semantic search quality is significantly better
5. **Annotate your views** — on-screen awareness unlocks cross-app workflows

---

## Related Sessions
- *Validate your App Intents adoption with App Intents testing* — unit testing intents in isolation
- *What's new in App Intents* — framework fundamentals

---

## Resources
- [Integrating your messaging app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-messaging-app-with-apple-intelligence)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [App schema domains](https://developer.apple.com/documentation/AppIntents/app-schema-domains)
- Session page: https://developer.apple.com/videos/play/wwdc2026/240/
