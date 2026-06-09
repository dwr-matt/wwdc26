# Build agentic app experiences with the Foundation Models framework

# WWDC 2026: Build Agentic App Experiences with the Foundation Models Framework

## Session Overview
This session introduces the **Dynamic Profiles** API, a new capability within the Foundation Models framework designed to help developers create "agentic" app experiences. The framework provides the tools necessary to manage context, establish model boundaries, and orchestrate complex agentic workflows. By using dynamic, re-evaluatable profiles, developers can seamlessly swap model personas, manage conversation history, and optimize for both cost and performance across different application states.

---

## Key Concepts, APIs, and Frameworks

### 1. Dynamic Profiles
A `DynamicProfile` allows developers to define the configuration state of a language model session. A profile encapsulates:
*   **Instructions:** Specific goals for the model.
*   **Tools:** Capabilities available to the model.
*   **Modifiers:** Configuration settings for temperature, sampling, and model selection.
*   **Dynamic Instructions:** Reusable, composable components that can group instructions and tools, which can then be nested or shared across the codebase.

### 2. Session Management
*   **Foundation Models Framework Utilities:** An open-source Swift package containing experimental patterns, modifiers, and components for agentic development.
*   **Session Properties:** A way to define state accessible across all tools and profiles. Developers can use the `@SessionPropertyEntry` macro to create custom state (e.g., storing conversation summaries) that persists across the session.
*   **Transcript Control:** The `History` property and `HistoryTransform` allow developers to prune, filter, or redact the model’s context window to stay within size limits or protect privacy.

### 3. Execution Control
*   **Tool Calling Modes:** Developers can now set the `ToolCallingMode` to `allowed` (default), `disallowed` (to prevent irrelevant tool usage), or `required` (to force the model into an action loop).
*   **Error Handling Policies:** The `TranscriptErrorHandlingPolicy` allows developers to choose between `revertTranscript` (automatic rollback on error) and `preserveTranscript` (manual control for complex recovery).

---

## Code Patterns & Orchestration Techniques

The session highlights two primary patterns for agent orchestration:

1.  **BatonPass (Collaboration):** Profiles share a persistent transcript history. A tool call from one profile triggers a handoff, changing the active profile variable to continue the conversation in a new persona while maintaining full context.
2.  **Phone-a-Friend (Consultation):** The parent session spawns a short-lived, isolated child session to perform a specific task (e.g., generating a title). The child returns the result as tool output, and the child session is then discarded, ensuring the parent maintains control of the final answer.

---

## Practical Takeaways for Developers

*   **Context Engineering:** Use `HistoryTransform` or `SessionProperties` to manage token limits. Prioritize `HistoryTransform` for lossless, profile-specific modifications, and `SessionProperties` for cross-profile shared state.
*   **Performance Awareness:** Be mindful of **KV Cache invalidation**. Modifying the transcript (removing entries or changing instructions) triggers cache invalidations, which increases latency. Always use the upgraded **Xcode Instruments** to profile your app's performance.
*   **Accuracy & Testing:** Because rewriting history can confuse a model, rely on the **Evaluations framework**. Create evaluation sets to quantify how your context engineering strategies impact model accuracy.
*   **Start Simple:** Leverage the `Foundation Models Framework Utilities` package for pre-built modifiers and patterns rather than building everything from scratch. 
*   **The "While Loop" Warning:** When using `required` tool calling mode, always ensure there is a clear, forced exit condition (such as a tool that throws an error) to prevent infinite loops.

<!-- resources -->

---

## Resources

- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/242/4/7f05515d-be1a-43a0-9962-a1f77f115666/downloads/wwdc2026-242_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/242/4/7f05515d-be1a-43a0-9962-a1f77f115666/downloads/wwdc2026-242_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/242/
