# Build with the new Apple Foundation Model on Private Cloud Compute — Session Notes

Code in this file is sourced from the official documentation pasted directly by the user:
https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute

---

## When to Use PCC

Start with the on-device model. Use the Evaluations framework to assess quality. Only switch to PCC if you need:
- Context larger than 4K (long documents, extended conversations)
- Stronger reasoning capability

---

## On-Device vs PCC

| Property | `SystemLanguageModel` | `PrivateCloudComputeLanguageModel` |
|---|---|---|
| Privacy | ✅ | ✅ |
| Works offline | ✅ | ❌ |
| Usage limits | Unlimited | Daily quota |
| Reasoning | Not supported | Multiple levels |
| Context size | 4K | 32K |

---

## Switching to PCC — One Line Change

Both `PrivateCloudComputeLanguageModel` and `SystemLanguageModel` conform to `LanguageModel`. All respond methods, tools, and instructions carry over unchanged.

```swift
// Create a session with the server-side model.
let session = LanguageModelSession(model: PrivateCloudComputeLanguageModel())
```

OS availability guard:

```swift
if #available(iOS 27.0, macOS 27.0, watchOS 27.0, visionOS 27.0, *) {
    // Create a session using the server-based model.
} else {
    // Use the on-device model on older versions.
}
```

---

## Check Availability Before Use

PCC requires Apple Intelligence support and a network connection.

```swift
let model = PrivateCloudComputeLanguageModel()

switch model.availability {
case .available:
    // Show your intelligence UI.
case .unavailable(.deviceNotEligible):
    // Show an alternative UI.
case .unavailable(.systemNotReady):
    // PCC isn't ready to serve requests.
case .unavailable(let other):
    // The model is unavailable for an unknown reason.
}
```

If the request fails due to no network, retry with the on-device model.

---

## Reasoning

The model generates internal reasoning text before producing a response. Reasoning segments appear in the transcript but not in the final response content — useful for debugging complex prompts.

| Level | Behavior |
|---|---|
| `.light` | Gathers a little extra context, lowest latency |
| `.moderate` | Deeper reasoning — recommended starting point for evaluation |
| `.deep` | Extensive analysis; reasoning text may be longer than the response itself |

```swift
let response = try await session.respond(
    to: "What are the tradeoffs in this architecture?",
    contextOptions: ContextOptions(reasoningLevel: .deep)
)
```

> ⚠️ Reasoning tokens count toward the 32K context limit.

Observe the `transcript` during the session to show the model's thinking progress in real-time — especially useful with `.deep` reasoning which can take time.

---

## Handling Usage Quotas

Do NOT show an alert (dismissible). Show persistent UI instead.

```swift
let model = PrivateCloudComputeLanguageModel()

// Show quota status label
if model.quotaUsage.isLimitReached {
    Text("Usage limit exceeded")
        .foregroundStyle(Color.red)
} else if case .belowLimit(let info) = model.quotaUsage.status {
    if info.isApproachingLimit {
        Text("Nearing usage limit")
            .foregroundStyle(Color.orange)
    }
}

// Provide an upgrade button
if let suggestion = model.quotaUsage.limitIncreaseSuggestion {
    Button("Show options") {
        suggestion.show()  // presents system UI for iCloud+ upgrade
    }
}
```

Error thrown when limit is exceeded: `PrivateCloudComputeLanguageModel.Error.quotaLimitReached(_:)`

Use `resetDate` to inspect when the quota refreshes.

---

## Testing Quota States in Xcode

Product → Scheme → Edit Scheme → Run → Options tab
→ **Simulated Apple Foundation Models Availability** dropdown
→ Select `Approaching Quota Usage Limit` or `Quota Usage Limit Reached`

---

## Eligibility

- Apps with **fewer than 2 million downloads**
- Must apply for the managed entitlement on the Apple Developer website

---

## Related Sessions
- What's new in the Foundation Models framework — overview
- Meet the Evaluations Framework — evaluate on-device vs PCC before deciding
- Build agentic app experiences with the Foundation Models framework — using both models together with Dynamic Profiles
- Debug and Profile Agentic App Experiences with Instruments

---

## Resources
- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- Session page: https://developer.apple.com/videos/play/wwdc2026/319/
