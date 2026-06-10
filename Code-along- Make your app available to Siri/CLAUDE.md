# Code-along: Make your app available to Siri — Session Notes

Sources:
- Transcript: detailed step-by-step walkthrough of ComicCal (cosmic calendar app)
- Documentation: calendar schema reference page (no code examples — schema only)
- Code patterns verified against prior session docs (IndexedEntity, appEntityIdentifier, etc.)

Note: Calendar-specific schema code (entity property names, intent parameter names) is from transcript description. The structural patterns (IndexedEntity, @Dependency, IntentParameter value state, SnippetView) are verified from prior session documentation fetches.

---

## Five-Step Integration Path

```
1. Define Entities (CalendarEntity, AttendeeEntity, EventEntity)
2. Donate Entities to Spotlight (CSSearchableIndex)
3. Implement OpenIntent (navigate to a specific entity)
4. Add On-Screen Awareness (two view modifiers)
5. Implement Create / Update / Delete Intents
```

---

## Step 1: Define Entities

### Xcode Code Snippets

Type `calendar_` in the editor → Xcode autocomplete shows all schemas in the Calendar domain:
- `calendar_calendar` → CalendarEntity scaffold
- `calendar_attendee` → AttendeeEntity scaffold
- `calendar_event` → EventEntity scaffold
- `calendar_createEvent`, `calendar_updateEvent`, `calendar_deleteEvents` → Intent scaffolds

### CalendarEntity (IndexedEntity)

```swift
@AppEntity(schema: .calendar.calendar)
struct CalendarEntity: IndexedEntity {
    static let defaultQuery = CalendarQuery()
    var id: UUID
    var title: String

    var displayRepresentation: DisplayRepresentation {
        DisplayRepresentation(
            title: "\(title)",
            image: .init(systemName: "calendar")
        )
    }
}

@MainActor
struct CalendarQuery: EntityQuery, EnumerableEntityQuery {
    @Dependency var calendarManager: CalendarManager

    func entities(for identifiers: [UUID]) async throws -> [CalendarEntity] {
        try await calendarManager.fetchCalendars(for: identifiers).map(\.entity)
    }

    // EnumerableEntityQuery: lets Siri offer all calendars as options
    func allEntities() async throws -> [CalendarEntity] {
        try await calendarManager.fetchAllCalendars().map(\.entity)
    }
}
```

`@Dependency`: App Intents' dependency injection. Register once at app launch; queries and intents receive the same shared instance rather than creating new ones.

`@MainActor` on the query: required when the dependency is main-actor-isolated.

### AttendeeEntity (TransientAppEntity)

```swift
@AppEntity(schema: .calendar.attendee)
struct AttendeeEntity: TransientAppEntity {
    var person: IntentPersonType  // system-standard person type, cross-app compatible
    var isOptional: Bool
    var status: AttendeeStatus    // schematized enum via calendar_attendeeStatus snippet
    var type: AttendeeType        // schematized enum via calendar_attendeeType snippet
}
```

**Why TransientAppEntity, not IndexedEntity?**
- An attendee represents a person's participation in a specific event, not the person themselves
- The same person can attend multiple events — indexing separately creates duplicate Spotlight results
- Attendees are always accessed through the event that contains them — no independent lookup needed
- Result: no query to write, no index to maintain

### EventEntity (IndexedEntity)

```swift
@AppEntity(schema: .calendar.event)
struct EventEntity: IndexedEntity {
    static let defaultQuery = EventQuery()
    var id: UUID
    var title: String
    var startDate: Date
    var endDate: Date
    var calendar: CalendarEntity        // composes CalendarEntity
    var attendees: [AttendeeEntity]     // composes AttendeeEntity array
    var notes: String?
    var location: EventLocation?        // union value: PlaceDescriptor or String
    var recurrence: CalendarRecurrenceRule?
    var status: EventStatus
    // Custom properties not in schema are also allowed:
    var isFavorite: Bool
}
```

Schema-required properties (title, startDate, etc.) must be populated. Schema-optional properties your app doesn't support (travelTime, virtualLocation, etc.) can stay unset.

---

## Step 2: Donate Entities to Spotlight

`IndexedEntity` only defines the shape — entities must also be actively donated:

```swift
// In CalendarManager, use a named index (not the default index)
private let searchableIndex = CSSearchableIndex(name: "ComicCal")

func createCalendar(...) async throws -> Calendar {
    // ... creation logic
    try? await searchableIndex.indexAppEntities([calendar.entity])
    return calendar
}

func updateCalendar(...) async throws {
    // ... update logic
    try? await searchableIndex.indexAppEntities([calendar.entity])
}

func deleteCalendar(id: UUID) async throws {
    // ... deletion logic
    try? await searchableIndex.deleteAppEntities(
        identifiers: [id],
        entityType: CalendarEntity.self
    )
}
```

Same pattern for events: index on create/update, delete from index on delete.

**Verification**: create a new calendar → swipe down for Spotlight → it appears with calendar icon and title.

---

## Step 3: Open Intent

Without an Open Intent, tapping an event result in Siri/Spotlight just opens the app's home screen.

```swift
struct OpenEventIntent: OpenIntent {
    static let title: LocalizedStringResource = "Open Event"

    @Parameter(title: "Event")
    var target: EventEntity

    @Dependency var navigationManager: NavigationManager

    func perform() async throws -> some IntentResult {
        await navigationManager.navigate(to: target)
        return .result()
    }
}
```

The system calls this when the user taps any event result in Spotlight or Siri, or says "open [event name]".

---

## Step 4: On-Screen Awareness (Two View Modifiers)

### List view (multiple entities visible at once)

```swift
List(events) { event in
    EventRowView(event: event)
        .appEntityIdentifier(EntityIdentifier(for: EventEntity.self, identifier: event.id))
}
```

### Detail view (one primary entity front and center)

```swift
EventDetailView(event: event)
    .userActivity("com.example.comiccal.viewEvent") { activity in
        activity.appEntityIdentifier = EntityIdentifier(
            for: EventEntity.self,
            identifier: event.id
        )
    }
```

With these two modifiers, users can say "email the people in **this** event" without naming the event title. Siri resolves the entity from the view.

---

## Step 5: Create / Update / Delete Intents

### Create

```swift
@AppIntent(schema: .calendar.createEvent)
struct CreateEventIntent: AppIntent {
    var title: String
    var startDate: Date
    var endDate: Date
    var calendar: CalendarEntity?
    var location: EventLocation?          // union value
    var recurrence: CalendarRecurrenceRule?
    var notes: String?

    @Dependency var calendarManager: CalendarManager

    @MainActor
    func perform() async throws -> some ReturnsValue<EventEntity> {
        let event = try await calendarManager.createEvent(title: title, ...)
        return .result(value: event.entity)
    }
}
```

### Update — IntentParameter Value State

Optional parameters in update intents have three states — a plain `nil` check is insufficient:

```swift
@AppIntent(schema: .calendar.updateEvent)
struct UpdateEventIntent: AppIntent {
    var event: EventEntity
    var title: String?
    var startDate: Date?
    var recurrence: CalendarRecurrenceRule?

    @Dependency var calendarManager: CalendarManager

    func perform() async throws -> some ReturnsValue<EventEntity> & ShowsSnippetView {
        // Use $recurrence.value (IntentParameter) to distinguish three cases:
        switch $recurrence.value {
        case .unset:
            // User didn't mention recurrence → leave it unchanged
            break
        case .set(nil):
            // User said "don't repeat this" → explicitly clear it
            updatedRecurrence = nil
        case .set(let value):
            // User said "repeat weekly" → update to new value
            updatedRecurrence = value
        }

        let updated = try await calendarManager.updateEvent(event: event, ...)
        return .result(value: updated.entity) {
            EventSnippetView(event: updated.entity)
        }
    }
}
```

This three-state pattern applies to any optional parameter where clearing is a meaningful action.

### Custom Snippet View

Add `ShowsSnippetView` to the return type, then provide a SwiftUI view in the result:

```swift
func perform() async throws -> some ReturnsValue<EventEntity> & ShowsSnippetView {
    // ... perform logic
    return .result(value: event.entity) {
        EventSnippetView(event: event.entity)  // your branded SwiftUI card
    }
}
```

Without this, Siri shows a generic card built from `displayRepresentation`. Keep snippet views lightweight.

### Delete

```swift
@AppIntent(schema: .calendar.deleteEvents)
struct DeleteEventIntent: AppIntent {
    var event: EventEntity
    var span: EventSpan?  // for recurring events: this occurrence or all

    @Dependency var calendarManager: CalendarManager

    func perform() async throws -> some IntentResult {
        try await calendarManager.deleteEvent(id: event.id, span: span)
        return .result()
    }
}
// Siri automatically adds a confirmation dialog before deletion
```

---

## API Quick Reference

| Goal | API |
|---|---|
| Siri understands app data | `@AppEntity(schema:)` |
| Semantic search | `IndexedEntity` + `indexAppEntities()` |
| Temporary sub-entity (no index/query) | `TransientAppEntity` |
| Cross-app person representation | `IntentPersonType` |
| Navigate to specific entity | `OpenIntent` with `target: EntityType` |
| One primary entity on screen | `.userActivity { activity.appEntityIdentifier = ... }` |
| Multiple entities in a list | `.appEntityIdentifier(EntityIdentifier(...))` |
| Custom Siri result card | `ShowsSnippetView` in return type |
| Distinguish update vs. clear | `$param.value` → `.unset` / `.set(nil)` / `.set(value)` |
| Shared dependency injection | `@Dependency` |
| All available options for a param | `EnumerableEntityQuery.allEntities()` |

---

## Related Sessions
- [Build intelligent Siri experiences with App Schemas](../Build%20intelligent%20Siri%20experiences%20with%20App%20Schemas/) — concepts behind this code-along
- *Validate your App Intents adoption with App Intents testing* — unit testing intents
- *Explore advanced App Intents features for Siri and Apple Intelligence* — deeper customization

---

## Resources
- [Integrating your calendar app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-calendar-app-with-apple-intelligence)
- [Calendar schema domain](https://developer.apple.com/documentation/AppIntents/app-schema-domain-calendar)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- Session page: https://developer.apple.com/videos/play/wwdc2026/344/
