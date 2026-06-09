# LLM search using Core Spotlight

## Session Summary: LLM Search using Core Spotlight (WWDC 2026)

### Overview
This session introduces the **Spotlight Search Tool**, a new feature in the Foundation Models framework that bridges Apple’s existing Core Spotlight indexing with Large Language Models (LLMs). Instead of developers manually writing complex search queries, they can now expose their app’s indexed content to a foundation model, allowing the model to perform "tool calling" to retrieve, reason over, and synthesize user-specific information. The session uses a "Hiking Trails" application as a case study to demonstrate how to build conversational search interfaces that are grounded in a user's local data.

---

### Key Concepts, APIs, and Frameworks
*   **Foundation Models Framework:** The core infrastructure used to integrate LLMs into apps. It provides the **Tool Protocol**, which allows models to interact with external data or take actions.
*   **Spotlight Search Tool:** A specialized tool that implements the Tool Protocol, allowing an LLM to query the Core Spotlight index. It is supported across iOS, iPadOS, macOS, and visionOS.
*   **Index Delegate Protocol:** Updated to include a method for recovering the full `CSSearchableItem` via unique identifier, allowing the LLM to access rich metadata (beyond the compact search index) for reasoning.
*   **Guidance Profiles:** Used to scope and constrain the model's search capabilities, preventing the LLM from attempting irrelevant operations (e.g., searching for "author" relationships in an app that doesn't track them).
*   **Pipeline Stages:** A powerful mechanism that allows the model to request multi-step operations—such as filtering, counting, or computing averages—over the result set directly within the Spotlight infrastructure.
*   **Evaluations Framework:** Tools for testing how well a model retrieves data and generates responses. It supports "result coverage" metrics and automated trajectory testing.

---

### Code Patterns & Techniques
*   **Tool Registration:** Adding `SpotlightSearchTool` to a language model session is achieved with minimal code by importing `CoreSpotlight` and `FoundationModels`.
*   **Customizing Search:** Developers can provide a custom configuration to the tool, such as specifying file sources or defining metadata attributes the model should consider.
*   **Managing UI Streams:** Since search results arrive as an `async` sequence of events (batches), developers should use **query tokens** associated with each reply to handle UI updates effectively, ensuring the interface stays in sync with the model's reasoning process.
*   **Generable Types:** Pipeline stages are defined as "generable types," meaning the model can decide when and how to invoke a custom computation stage based on the natural language user request.
*   **Evaluation Suites:** Developers are encouraged to use the `ModelSample` protocol. This involves creating JSON-based datasets that include:
    *   **Query:** The natural language user prompt.
    *   **Trajectory Expectation:** A record of whether the tool was invoked.
    *   **Expected Results:** A set of `CSSearchableItem` identifiers that the model should have retrieved.

---

### Practical Takeaways for Developers
1.  **Prioritize Donation:** The quality of the LLM’s response is directly dependent on the richness of the data donated to Core Spotlight. Ensure your app is already donating searchable items effectively before implementing the search tool.
2.  **Use the Index Delegate for Metadata:** If your LLM responses seem shallow, it is likely because the search index uses a compact data representation. Implement the new `searchableItems(for:identifiers:)` delegate method to provide the full metadata required for high-quality LLM reasoning.
3.  **Optimize for On-Device Context:** On-device models have limited context windows. Use **Guidance Profiles** to focus the model on only the most relevant attributes, reducing noise and improving performance.
4.  **Adopt Pipeline Stages for Compute:** Do not attempt to pull massive datasets into the model's memory for calculation. If you need to perform calculations (e.g., "average miles per month"), write a custom pipeline stage to let the system handle the computation efficiently on the result set.
5.  **Focus on Coverage:** Use the Evaluations framework early to identify gaps. If the model isn't returning the expected items, refine your searchable item metadata rather than trying to "prompt engineer" the model into finding missing data.

<!-- resources -->

---

## Resources

- [Spotlight search tool](https://developer.apple.com/documentation/CoreSpotlight/Spotlight-search-tool)
- [Making your indexed content available to Foundation Models](https://developer.apple.com/documentation/CoreSpotlight/making-your-indexed-content-available-to-foundation-models)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/246/4/b390ab9d-d231-4cf5-9d1b-e4270ef5012b/downloads/wwdc2026-246_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/246/4/b390ab9d-d231-4cf5-9d1b-e4270ef5012b/downloads/wwdc2026-246_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/246/
