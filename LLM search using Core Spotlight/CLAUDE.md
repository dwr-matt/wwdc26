# LLM Search using Core Spotlight — Session Notes

Sources:
- Transcript: full walkthrough (Jennifer, Spotlight Engineering team), "Hiking Trails" sample app as case study
- Documentation fetches:
  - https://developer.apple.com/tutorials/data/documentation/corespotlight/spotlight-search-tool.json — fetched twice; first pass returned only type definitions (no code), second pass returned verified Swift code examples (below). Confirms types: `SpotlightSearchTool`, `SpotlightSearchTool.Configuration`, `SearchSource`, `CoreSpotlightSource`, `FileSource`, `SearchableItemAttribute`, `CustomStage`, `SearchPipelineData`, `SearchPipelineDataType`, `ScoredSearchableItem`, `SearchTextResult`, `SearchCount`, `SearchStatistic`, `SearchResultsTable`, `ContactResolver`, `ResolvedContact`, `GuidanceLevel` (`.complete`/`.focused(_:)`/`.dynamic(_:)`), `FormatLevel.compact` — all beta.
  - https://developer.apple.com/tutorials/data/documentation/corespotlight/making-your-indexed-content-available-to-foundation-models.json — fetched but returned no code; the Index Delegate `searchableItems(for:identifiers:)` method described in the transcript was NOT found in this doc.
- Index Delegate Protocol's `searchableItems(for:identifiers:)`, Guidance Profile exact syntax, and the Evaluations/`ModelSample` workflow are **transcript-described only** — no verified code.

---

## 1. What It Is / Why It Exists

**SpotlightSearchTool** — a new `Tool` (Foundation Models Tool Protocol) that lets an LLM directly query an app's existing Core Spotlight search index for contextual response generation. Available on iOS, iPadOS, macOS, visionOS.

Prerequisite: app must already donate searchable items to Core Spotlight (see prior session "supporting semantic search with Core Spotlight" — not covered here).

Core idea: stop writing custom search queries — provide content to Spotlight, let the model decide when/how to search and reason over results via tool calling.

---

## 2. Basic Usage (verified)

```swift
import CoreSpotlight
import FoundationModels

let tool = SpotlightSearchTool()
let session = LanguageModelSession(tools: [tool])
let response = try await session.respond(to: "Find my notes about the project deadline.")
```

Trajectory (transcript-described): model decides to call the tool → generates a query → Spotlight executes and returns a result-set description → model reasons over output → generates final response.

---

## 3. Contact Resolver — Reference Resolution (verified)

```swift
let tool = SpotlightSearchTool(configuration: .init(
    contactResolver: MyContactResolver()
))
let session = LanguageModelSession(tools: [tool])

// "I" resolves to the user's identity, matched against item author/recipient metadata
let response = try await session.respond(to: "Show me the documents I shared last week.")
```

`ContactResolver` returns contact info tied to the user's identity, matched against search index metadata (e.g. for "who is X" references in prompts).

---

## 4. Custom Pipeline Stages (verified)

For complex queries (e.g. "how many trails did I hike this year, and what's the average miles per month?"), the model can request Spotlight run a pipeline of search + computation stages instead of loading the full result set into context.

```swift
struct RecencyBoostStage: CustomStage {
    static var name: String { "recency_boost" }
    static var description: String { "Boosts recently modified items in the ranking." }
    static var inputTypes: [SearchPipelineDataType] { [.items] }
    static var outputTypes: [SearchPipelineDataType] { [.scoredItems] }

    var recencyWeight: Double

    func execute(items: [CSSearchableItem]) async throws -> SearchPipelineData {
        let now = Date()
        let scored = items.map { item -> ScoredSearchableItem in
            let age = now.timeIntervalSince(item.attributeSet.contentModificationDate ?? .distantPast)
            let recencyScore = max(0, 1.0 - (age / (30 * 86400)))
            return ScoredSearchableItem(item: item, score: recencyScore * recencyWeight)
        }
        return .scoredItems(scored)
    }
}

extension CustomStage where Self == RecencyBoostStage {
    static func recencyBoost(weight: Double = 0.3) -> Self {
        RecencyBoostStage(recencyWeight: weight)
    }
}

let tool = SpotlightSearchTool(configuration: .init(
    sources: [.coreSpotlight],
    customStages: [.recencyBoost(), .recencyBoost(weight: 0.5)]
))
```

Pipeline stages are **generable** — the model decides when/whether to invoke them based on the natural-language query. Transcript example: a "happiness score" stage running sentiment analysis over a notes attribute, so the model answers "which hikes made me happiest?" using only top-scored results.

---

## 5. Monitoring Search Results / UI Display (verified)

Results stream as an async sequence in batches; use `queryToken` to detect a new query round (model may call the tool multiple times per response):

```swift
let tool = SpotlightSearchTool()
let session = LanguageModelSession(tools: [tool])

Task {
    var currentToken: SpotlightSearchTool.SearchReply.QueryToken?
    for await reply in tool.searchResults {
        if reply.queryToken != currentToken {
            currentToken = reply.queryToken
        }
        switch reply.content {
        case .items(let items):
            displayResultsList(label: reply.label, items: items)
        case .count(let n, let header):
            displayCount(n, header: header ?? reply.label)
        case .table(let table):
            displayTable(table)
        case .statistic(let name, let value, let header):
            displayMetric(name: name, value: value, header: header ?? reply.label)
        case .text(let body, let header):
            displayTextBlock(body, header: header ?? reply.label)
        }
        showProgressIndicator(reply.status == .partial)
    }
}

let response = try await session.respond(to: "Show me recent emails from Shelly.")
```

Reply content types: items list, count, table, statistic (sum/avg/max/min/median/stddev), free text — each carries an LLM-generated `label`.

---

## 6. Guidance Profiles (transcript-described, types verified)

By default the tool exposes its full search capability set (semantic text search + structured search over dates/people/locations) to guided generation. Guidance profiles scope this to only what the app needs — e.g. skip "search by author/recipient" guidance if the app doesn't donate person relationships. Especially important for on-device models with smaller context windows.

Verified types: `GuidanceLevel` with cases `.complete`, `.focused(_:)`, `.dynamic(_:)`; `FormatLevel.compact` (for smaller-context models). Exact configuration syntax not found in fetched docs.

---

## 7. Index Delegate: Recovering Full Metadata (transcript-described, NOT found in fetched docs)

Some Spotlight-indexed content (text bodies, HTML) is stored in a compact, search-only representation the model can't read back, leading to shallow responses. Fix described in transcript: implement a new Index Delegate method `searchableItems(for:identifiers:)` to return the full `CSSearchableItem` by identifier, giving the model richer metadata. Also a good place to attach extra attributes not worth donating for search but useful for model reasoning. **No verified code for this method was found** in `making-your-indexed-content-available-to-foundation-models.json`.

---

## 8. Evaluations Framework — Result Coverage (transcript-described)

Workflow:
1. Define a dataset conforming to `ModelSample` protocol: natural-language query, expected trajectory (was the tool called), set of expected `CSSearchableItem` identifiers, optional sample response
2. Use sample generation APIs to expand seed samples into broader coverage
3. Run evaluation: donate items to Core Spotlight, configure `SpotlightSearchTool`, run, check **result coverage** metric (did the response include the expected items?)

Guidance: if expected items are missing, fix the searchable metadata/donation — don't just prompt-engineer.

See also: *Create robust evaluations for agentic apps*, *Meet the Evaluations framework*.

---

## Related Sessions
- *Supporting semantic search with Core Spotlight* (prior year, donation/indexing basics — not covered here)
- *Bring an LLM provider to the Foundation Models framework* — Tool Protocol fundamentals
- *Build agentic app experiences with the Foundation Models framework* — guided generation, generable types
- *Create robust evaluations for agentic apps* / *Meet the Evaluations framework*

---

## Resources
- [Spotlight search tool](https://developer.apple.com/documentation/CoreSpotlight/Spotlight-search-tool)
- [Making your indexed content available to Foundation Models](https://developer.apple.com/documentation/CoreSpotlight/making-your-indexed-content-available-to-foundation-models)
- Session page: https://developer.apple.com/videos/play/wwdc2026/246/
