# Explore advanced App Intents features for Siri and Apple Intelligence — Session Notes

Sources:
- Transcript: full walkthrough using CosmoTunes, Unicorn Chat, and ComicCal sample apps
- Documentation fetches: returned only index pages with no code examples
- Code patterns described below are from transcript narration — structural patterns (IntentDialog, requestValue, etc.) are inferred from description. Treat as conceptual reference until verified against sample code.

---

## Six Topics

1. Custom Siri conversation (dialog, requestValue, SnippetView)
2. Interaction donations (teaching Apple Intelligence UI habits)
3. EntityOwnershipProviding (confirmation for shared data)
4. Content discovery (Spotlight, IntentValueQuery, systemSearchInApp)
5. On-Screen Awareness (four APIs + displayRepresentations optimization)
6. System integration annotations (notifications, Now Playing, AlarmKit)

---

## 1. Custom Siri Conversation

### ProvidesDialog — Custom Response Voice

Default: Siri auto-generates a response. Add `ProvidesDialog` to match your app's personality:

```swift
// Structural pattern from transcript — not verified against docs
func perform() async throws -> some IntentResult & ProvidesDialog {
    addSong(to: playlist)
    return .result(dialog: IntentDialog(
        full: "Added \(song.title) to your \(playlist.name) mixtape.",
        supporting: "Added to \(playlist.name)"
    ))
}
```

- `full`: read aloud on voice-only devices (AirPods) — must stand alone without visuals
- `supporting`: shown in UI alongside visuals — can be shorter

### requestValue — Ask a Question Mid-Intent

Interrupt `perform()` to request a missing parameter:

```swift
// Structural pattern from transcript — not verified against docs
func perform() async throws -> some IntentResult & ProvidesDialog {
    if timerAlreadyRunning && label == nil {
        label = try await $label.requestValue(
            "There's already a timer running. Give this one a name."
        )
    }
    // ... continue ...
}
```

Use sparingly — unnecessary clarifying questions create friction. Sample code has other dialog request types (choice list, confirmation).

### Entity DisplayRepresentation

Used everywhere: Siri response cards, disambiguation, Spotlight, Shortcuts UI.

```swift
var displayRepresentation: DisplayRepresentation {
    DisplayRepresentation(
        title: "\(song.title)",
        subtitle: "\(song.artist)",
        image: .init(data: song.albumArtData)
    )
}
```

**Start here first** — highest return on investment of any single change.

### SnippetView — Custom Siri Result Card

```swift
func perform() async throws -> some ReturnsValue<PlaylistEntity> & ShowsSnippetView {
    // ...
    return .result(value: playlist.entity) {
        PlaylistSnippetView(playlist: playlist)
    }
}
```

Keep snippet views lightweight. Scale across the ecosystem.

---

## 2. Interaction Donations

Apple Intelligence learns from Siri/Shortcuts interactions automatically. It **cannot** learn from UI-only actions without your help.

Pattern: add a `donateIntent` flag to shared helpers so you only donate UI interactions:

```swift
// Structural pattern from transcript — not verified against docs
func sendMessage(to recipients: [Contact], content: String, donateIntent: Bool = false) async throws {
    // ... send logic ...

    if donateIntent {  // only donate from UI calls, not Siri calls
        var intent = SendMessageIntent()
        intent.destination = .recipients(recipients.map(\.entity))
        intent.content = AttributedString(content)

        Task {
            try await IntentDonationManager.shared.donate(
                intent: intent,
                result: .result(value: [sentMessage.entity])
            )
        }
    }
}
```

**Rules:**
- Only donate UI interactions — Siri interactions are already recorded
- Don't donate excessively — system may ignore over-donated intents
- Donate accurately — reflects real user behavior

**Ongoing activity pattern**: If a user starts navigation in your Maps-domain app via UI, donate that intent. Then when they ask Siri "add a stop," Siri can find the active navigation session. Applies to: Maps navigation start/stop, Clock stopwatch start/stop/pause/lap.

---

## 3. EntityOwnershipProviding — Confirmation for Shared Data

LLMs carry unintended side-effect risk. Siri auto-confirms actions on shared/public entities:

- Default: Siri assumes entities are private → may skip confirmation
- `.shared` / `.public`: Siri shows confirmation dialog before mutating actions

```swift
// Structural pattern from transcript — not verified against docs
extension EventEntity: EntityOwnershipProviding {
    var ownershipState: EntityOwnershipState {
        attendees.isEmpty ? .private : .shared
    }
}
```

Only add this protocol to entities people can actually share in your app. Keep `ownershipState` up to date whenever the system queries the entity. Siri uses `displayRepresentation` in the confirmation UI — another reason to invest in it early.

---

## 4. Content Discovery: Three Paths

### Path A: IndexedEntity + Spotlight (best quality)

Semantic search. Good for local data, moderate dataset size.

Keep the index maintained:
- Create → `indexAppEntities()`
- Update (especially properties used in `displayRepresentation`) → re-index
- Delete → `deleteAppEntities()`
- Spotlight requests reindex → implement `IndexedEntityQuery.reindexAllEntities()`

### Path B: IntentValueQuery (dynamic/large/server-side data)

Receives structured search input from system; you search and return matching entities. Can return multiple entity types via `@UnionValue`:

```swift
// Structural pattern from transcript — not verified against docs
struct AudioEntityQuery: IntentValueQuery {
    func values(for audioSearch: AudioSearch) async throws -> [AudioEntity] {
        switch audioSearch.criteria {
        case .searchQuery(let query):
            return await searchSongs(matching: query).map { AudioEntity.song($0) }
        case .unspecified:
            // "play CosmoTunes" with no specifics → play recently liked
            return await fetchRecentlyLiked().map { AudioEntity.song($0) }
        case .url(let url):
            // "play that playlist Glow sent me"
            return await findEntity(for: url).flatMap { [AudioEntity.playlist($0)] } ?? []
        }
    }
}
```

`AudioEntity` is a `@UnionValue` enum wrapping `.song(SongEntity)` or `.playlist(PlaylistEntity)`.

### Path C: systemSearchInApp Schema

Opens your app's own search UI with the query. Works regardless of which domains you adopt or whether you index:

```swift
// Structural pattern from transcript — not verified against docs
@AppIntent(schema: .system.searchInApp)
struct SearchInAppIntent: AppIntent {
    var searchQuery: String

    func perform() async throws -> some IntentResult {
        await navigationManager.openSearch(query: searchQuery)
        return .result()
    }
}
```

"Show me running playlists in CosmoTunes" → opens the app's search screen with results.

---

## 5. On-Screen Awareness

### Four APIs

| Scenario | API |
|---|---|
| One primary entity on screen | `NSUserActivity` + `appEntityIdentifier` |
| Each row in a list | `.appEntityIdentifier(EntityIdentifier(...))` view modifier |
| Large list (lazy, tracks selections that scroll off-screen) | Collection annotation (SwiftUI: `.appEntityIdentifier(for:selection:)` on List) |
| Canvas / custom non-standard views | `appEntityUIElements` / `AppEntityUIElementProvider` |

Collection annotations avoid the overhead of attaching an annotation to every single row. They also preserve selected entities that scroll off-screen — per-row annotations disappear when the view leaves the hierarchy.

UIKit equivalents: `AppEntityAnnotatable`, `UICollectionViewAppIntentsDataSource`, `AppEntityUIElementProvider`.

### displayRepresentations Optimization

When Siri needs to understand many on-screen entities quickly, querying full entities is expensive. Implement `displayRepresentations(for:)` so Siri can get just the text:

```swift
// Structural pattern from transcript — not verified against docs
extension PlaylistQuery: EntityQuery {
    func entities(for identifiers: [UUID]) async throws -> [PlaylistEntity] {
        try await store.fetchPlaylists(ids: identifiers)  // full fetch
    }

    // Siri calls this for on-screen understanding — much cheaper
    func displayRepresentations(for identifiers: [UUID]) async throws
        -> [EntityIdentifier: DisplayRepresentation] {
        try await store.fetchPlaylistMetadata(ids: identifiers)
            .reduce(into: [:]) { dict, playlist in
                dict[EntityIdentifier(for: PlaylistEntity.self, identifier: playlist.id)] =
                    DisplayRepresentation(title: "\(playlist.name)")
            }
    }
}
```

Without this, Siri may fail to understand on-screen content fast enough and ask to clarify or act on something else.

---

## 6. System Integration Annotations

Connect your entities to existing system integrations. **Important (from transcript): cannot use `TransientAppEntity` — these require persistent identifiers.**

### UNMutableNotificationContent

```swift
// Pattern from transcript
content.appEntityIdentifiers = [
    EntityIdentifier(for: MessageEntity.self, identifier: message.id)
]
// → When AirPods announce the notification, user can say "reply"
```

### MediaSessionRepresentable (Now Playing)

Add entity identifiers ordered from most specific to least specific:

```swift
// Pattern from transcript
attributes.appEntityIdentifiers = [
    EntityIdentifier(for: SongEntity.self, identifier: song.id),       // most specific
    EntityIdentifier(for: ArtistEntity.self, identifier: artist.id),
    EntityIdentifier(for: PlaylistEntity.self, identifier: playlist.id) // least specific
]
// → Enables contextual requests like "play the live version"
```

### AlarmConfiguration (AlarmKit)

```swift
// Pattern from transcript
config.appEntityIdentifier = EntityIdentifier(for: SongEntity.self, identifier: song.id)
// → User can say "snooze it" when the alarm fires
```

---

## Development Priority Order (from transcript)

1. **DisplayRepresentation** — affects everywhere, highest ROI
2. **Semantic index + keep it current** — foundation for Siri finding your content
3. **IntentValueQuery + systemSearchInApp** — covers non-indexed content
4. **View annotations** — on-screen awareness
5. **Interaction donations** — personalization
6. **System annotations** (notifications, Now Playing, AlarmKit) — polish

---

## Related Sessions
- [Build intelligent Siri experiences with App Schemas](../Build%20intelligent%20Siri%20experiences%20with%20App%20Schemas/) — concepts
- [Code-along: Make your app available to Siri](../Code-along-%20Make%20your%20app%20available%20to%20Siri/) — step-by-step
- [Discover new capabilities in the App Intents framework](../Discover%20new%20capabilities%20in%20the%20App%20Intents%20framework/) — new APIs
- *Secure your app: Mitigate risks to agentic features* — trust and safety

---

## Resources
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Donating your app's data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- Session page: https://developer.apple.com/videos/play/wwdc2026/343/
