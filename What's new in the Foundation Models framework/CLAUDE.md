# What's New in the Foundation Models Framework — Session Notes

This is the WWDC26 overview session for Foundation Models. Three major themes: new models, new system tools, new agentic APIs.

---

## 1. Model Updates

### New On-Device Model
- Rebuilt from scratch: better logic, better tool calling
- New **Vision capability** — pass images directly into prompts, no preprocessing needed

```swift
let response = try await session.respond(to: {
    ImageAttachment(image: myUIImage)
    "What technique was used in this origami?"
})
```

Supported image types: `UIImage`, `NSImage`, `CGImage`, `CIImage`, `CVPixelBuffer`, File URL. Any size/aspect ratio accepted.

---

### Private Cloud Compute (PCC) Language Model
Server-side model powering many Apple Intelligence features.

| Property | Value |
|---|---|
| Context window | 32,000 tokens |
| Reasoning | `reasoningLevel` parameter (none / low / deep) |
| Privacy | Prompts never stored, independently verifiable |
| Auth | No API key, no account setup needed |
| Cost | Free for < 2M first-time downloads; iCloud+ users get higher limits |

```swift
let session = LanguageModelSession(model: PrivateCloudComputeLanguageModel())
let response = try await session.respond(
    to: "Plan this project",
    options: ContextOptions(reasoningLevel: .deep)
)
```

PCC also enables Foundation Models on **watchOS 27** for the first time.

---

### Language Model Protocol (Model Abstraction Layer)
New `LanguageModel` protocol — any model can back a `LanguageModelSession`.

```swift
import AnthropicFoundationModels
let session = LanguageModelSession(model: ClaudeModel())

import GoogleFoundationModels
let session = LanguageModelSession(model: GeminiModel())
```

Available implementations:
- `SystemLanguageModel` — on-device
- `PrivateCloudComputeLanguageModel` — Apple server
- `CoreAILanguageModel` — open source, runs local models on Apple Neural Engine
- `MLXLanguageModel` — open source, runs local models on Mac GPU
- Anthropic & Google Swift packages (third-party)

> ⚠️ Third-party models: never hardcode API keys. Use OAuth + Keychain.

---

### Token Usage Tracking
```swift
let response = try await session.respond(to: prompt)
response.usage.inputTokens        // total input tokens
response.usage.cachedInputTokens  // tokens served from cache
response.usage.reasoningTokens    // tokens spent on reasoning
```

---

## 2. System Tools

Built-in tools, no implementation needed — just attach to a session.

| Tool | Purpose |
|---|---|
| `BarcodeReaderTool` | Model reads barcodes/QR codes from images |
| `OCRTool` | Extracts structured text from images |
| `SpotlightSearchTool` | Local RAG — searches user's files via Spotlight index |

Spotlight RAG was the most-requested feature. No custom vector database needed.

```swift
let session = LanguageModelSession(tools: [SpotlightSearchTool()])
```

---

## 3. New APIs

### Dynamic Profiles
Overview only — see *Build agentic app experiences with the Foundation Models framework* for the deep dive and detailed notes.

One `LanguageModelSession` dynamically switches between modes. Each mode has its own instructions, tools, and model config. Change a variable → next prompt uses the new config.

```swift
@DynamicProfile
var profile: some DynamicProfile {
    switch mode {
    case .analysis:
        "Analyze the craft in the image"
        AnalysisTools()
        Model(.system)
    case .brainstorming:
        "Suggest creative project ideas"
        BrainstormTools()
        Model(.privateCloudCompute)
        ReasoningLevel(.deep)
    }
}
```

---

### Evaluations Framework
New Swift framework for measuring AI feature quality. Quantifies the statistical impact of prompt changes — enables data-driven confidence in non-deterministic features. See *Meet the Evaluations Framework* for details.

---

## 4. Mac Tooling

### FM CLI
Terminal access to on-device and PCC models.

```bash
fm chat
fm "what does valley fold mean in origami?"
fm --image photo.jpg "generate a descriptive filename based on image content"
```

### Python SDK
Same on-device model, accessible from Python for data scientists and researchers.

```python
from foundation_models import LanguageModel
response = LanguageModel().generate("Explain the valley fold technique")
```

---

## 5. Open Source

| Package | Status |
|---|---|
| Foundation Models Framework core | Open source |
| Foundation Models Framework Utilities | Open source, updated between OS releases |
| CoreAI Language Model | Open source |
| MLX Language Model | Open source |

Framework now runs anywhere Swift runs, including **Linux servers**.

---

## Related Sessions
- [Build agentic app experiences with the Foundation Models framework](../Build%20agentic%20app%20experiences%20with%20the%20Foundation%20Models%20framework/) — Dynamic Profiles deep dive
- Build with the new Apple Foundation Model on Private Cloud Compute — PCC entitlement details
- Meet the Evaluations Framework
- Bring an LLM Provider to the Foundation Models Framework
- Build AI-Powered Scripts with the FM CLI and Python SDK
- LLM search using Core Spotlight

---

## Resources
- [Expanding generation with tool calling](https://developer.apple.com/documentation/FoundationModels/expanding-generation-with-tool-calling)
- [Analyzing images with multimodal prompting](https://developer.apple.com/documentation/FoundationModels/analyzing-images-with-multimodal-prompting)
- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- Session page: https://developer.apple.com/videos/play/wwdc2026/241/
