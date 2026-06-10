# What's New in the Foundation Models Framework — Session Notes

Code sourced from official documentation via JSON API:
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/analyzing-images-with-multimodal-prompting.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/expanding-generation-with-tool-calling.json
- https://developer.apple.com/tutorials/data/documentation/foundationmodels/adding-server-side-intelligence-with-private-cloud-compute.json (content via user paste)

---

## Overview: Three Major Themes

1. **New models** — upgraded on-device model + PCC server model + third-party model protocol
2. **New system tools** — Vision tools (Barcode, OCR) + Spotlight RAG
3. **New APIs** — Dynamic Profiles, Evaluations Framework, FM CLI, Python SDK

---

## 1. On-Device Model: Vision (Multimodal)

Pass images directly into prompts using `Attachment`. No preprocessing or cropping required.

```swift
func compareImages(imageOne: CGImage, imageTwo: CGImage) async throws -> String {
    let session = LanguageModelSession()
    let response = try await session.respond {
        "Compare these two images by using three bullet points:"
        Attachment(imageOne)
        // When the image doesn't have rotation applied (e.g. from AVFoundation),
        // use orientation to perform a transform before sending to the model.
        Attachment(imageTwo, orientation: .right)
    }
    return response.content
}
```

Image classification with structured output:

```swift
@Generable
enum ImageLabel {
    case cat
    case dog
    case frog
    case bird
}

func classifyImage(_ image: CGImage) async throws -> ImageLabel {
    let session = LanguageModelSession()
    let response = try await session.respond(
        generating: ImageLabel.self,
        options: GenerationOptions(samplingMode: .greedy)
    ) {
        "Choose the label that best represents the following image:"
        Attachment(image)
    }
    return response.content
}
```

Supported image types: `CGImage`, `CIImage`, `CVPixelBuffer`, image URLs.

---

## 2. Private Cloud Compute (PCC) Language Model

See full notes in the PCC session CLAUDE.md. Quick reference:

```swift
// Change one line to switch to PCC
let session = LanguageModelSession(model: PrivateCloudComputeLanguageModel())
```

| Property | SystemLanguageModel | PrivateCloudComputeLanguageModel |
|---|---|---|
| Works offline | ✅ | ❌ |
| Usage limits | Unlimited | Daily quota |
| Reasoning | ❌ | ✅ (light / moderate / deep) |
| Context size | 4K | 32K |

---

## 3. Language Model Protocol (Third-Party Models)

`PrivateCloudComputeLanguageModel` and `SystemLanguageModel` both conform to `LanguageModel`. Third-party providers (Anthropic, Google) publish Swift packages that also conform. Usage is identical:

```swift
import AnthropicFoundationModels
let session = LanguageModelSession(model: ClaudeModel())
```

> ⚠️ Third-party models: never hardcode API keys. Use OAuth + Keychain.

---

## 4. System Tools

### Barcode Reader + OCR (Vision-backed)

```swift
func analyzeBarcodeImage(_ image: CGImage) async {
    do {
        let session = LanguageModelSession(tools: [BarcodeReaderTool()])
        let response = try await session.respond {
            """
            Scan this image for any barcodes. For each barcode found, describe \
            its symbology type and explain what the encoded content means or \
            represents.
            """
            Attachment(image)
                .label("barcode-image")
        }.content
        print("The model response: \(response)")
    } catch {
        // Handle the error.
    }
}
```

Built-in tools:
- `BarcodeReaderTool` — scans machine-readable codes from images
- `OCRTool` — extracts structured text from images

### Spotlight Search (Local RAG)

```swift
let session = LanguageModelSession(tools: [SpotlightSearchTool()])
```

Gives the model access to the user's local files via Spotlight index. No custom vector database needed.

---

## 5. Tool Calling

### Define a Tool

```swift
struct BreadDatabaseTool: Tool {
    let name = "searchBreadDatabase"
    let description = "Searches a local database for bread recipes."

    @Generable
    struct Arguments {
        @Guide(description: "The type of bread to search for")
        var searchTerm: String
        @Guide(description: "The number of recipes to get", .range(1...6))
        var limit: Int
    }

    func call(arguments: Arguments) async throws -> [String] {
        var recipes: [Recipe] = []
        // retrieve recipes from your database
        return recipes.map { "Recipe for '\($0.name)': \($0.description) Link: \($0.link)" }
    }
}
```

### Use the Tool

```swift
let session = LanguageModelSession(tools: [BreadDatabaseTool()])
let response = try await session.respond(to: "Find three sourdough bread recipes")
```

### Tool Calling Modes

```swift
// Force the model to call a tool
let response = try await session.respond(
    to: "What's a good sourdough recipe?",
    options: GenerationOptions(toolCallingMode: .required)
)

// Prevent tool calls
let response = try await session.respond(
    to: "Summarize the recipes you found",
    options: GenerationOptions(toolCallingMode: .disallowed)
)
```

### Error Handling

```swift
do {
    let answer = try await session.respond(to: "Find a recipe for tomato soup.")
} catch let error as LanguageModelSession.ToolCallError {
    print(error.tool.name)
    if case .databaseIsEmpty = error.underlyingError as? SearchBreadDatabaseToolError {
        // Display an error in the UI.
    }
} catch {
    print("Some other error: \(error)")
}
```

### Transcript Inspection

```swift
struct MyHistoryView: View {
    @State var session = LanguageModelSession(tools: [BreadDatabaseTool()])

    var body: some View {
        List(session.transcript) { entry in
            switch entry {
            case .instructions(let instructions): ...
            case .prompt(let prompt): ...
            case .toolCalls(let calls): ...
            case .toolOutput(let output): ...
            case .response(let response): ...
            case .reasoning(let reasoning): ...
            }
        }
    }
}
```

---

## 6. Dynamic Profiles (overview)

See full notes in *Build agentic app experiences* CLAUDE.md for deep dive.

---

## 7. Token Usage Tracking

Verbal description from transcript only — no verified code available.
Sessions and responses have a `usage` property: `inputTokens`, `cachedInputTokens`, `reasoningTokens`.

---

## 8. FM CLI & Python SDK

Verbal description from transcript — no verified code available.
- `fm chat` — interactive terminal access to on-device and PCC models
- Python SDK: `from foundation_models import LanguageModel`

---

## Open Source

Foundation Models core framework, Utilities package, CoreAI Language Model, MLX Language Model — all open source. Runs anywhere Swift runs including Linux.

---

## Related Sessions
- [Build agentic app experiences with the Foundation Models framework](../Build%20agentic%20app%20experiences%20with%20the%20Foundation%20Models%20framework/)
- [Build with the new Apple Foundation Model on Private Cloud Compute](../Build%20with%20the%20new%20Apple%20Foundation%20Model%20on%20Private%20Cloud%20Compute/)
- Meet the Evaluations Framework
- Bring an LLM Provider to the Foundation Models Framework
- LLM search using Core Spotlight

---

## Resources
- [Analyzing images with multimodal prompting](https://developer.apple.com/documentation/FoundationModels/analyzing-images-with-multimodal-prompting)
- [Expanding generation with tool calling](https://developer.apple.com/documentation/FoundationModels/expanding-generation-with-tool-calling)
- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- Session page: https://developer.apple.com/videos/play/wwdc2026/241/
