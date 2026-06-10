# Debug and profile agentic app experiences with Instruments

# Summary: Debug and profile agentic app experiences with Instruments (WWDC 2026)

## Overview
This session introduces developers to the **Foundation Models instrument** in Xcode, a powerful tool designed to demystify and debug agentic AI applications. Because Large Language Models (LLMs) are non-deterministic, traditional debugging techniques like unit testing are often insufficient. The session demonstrates how to use the Foundation Models instrument to gain visibility into the "chain of thought" within multi-model pipelines, identify silent failures in tool-calling loops, and optimize for performance metrics like latency and token usage.

---

## Key Concepts, APIs, and Frameworks
*   **Foundation Models Framework:** The core API for accessing on-device and server-based generative AI. It enables features that adapt to context using dynamic instructions and tool-calling capabilities.
*   **Agentic Experiences:** Apps that don't just generate text but "reason" through prompts, call tools to perform actions, and loop until a final goal is reached.
*   **Three Core Challenges of LLM Development:**
    1.  **Probabilistic Output:** Outputs are non-deterministic, making hard-coded assertions in unit tests ineffective; quality must be evaluated based on intent.
    2.  **Model-to-Model Communication:** The complexity of orchestrating multiple models and handling data hand-offs.
    3.  **Observability:** The difficulty of tracing *why* a model made a specific decision or where a multi-step pipeline failed.
*   **Instrumentation Metrics:**
    *   **Time to First Token:** Critical for perceived responsiveness; can be improved by shortening prompts.
    *   **Tokens per Second:** Used for benchmarking generation speed.
    *   **Total Latency:** The end-to-end time; can be improved by utilizing streaming to show partial results.

---

## Demonstrated Techniques
*   **Profiling with the Foundation Models Template:** Developers can launch their app via the "Profile" option in Xcode and select the dedicated template to record live sessions. The tool logs interactions, including prompts, responses, tool calls, and instruction switches.
*   **The Tree Hierarchy View:** The instrument organizes trace data into a structured hierarchy:
    *   **Sessions → Requests → Model Inferences → Instructions → Prompts/Responses.**
*   **Debugging Logic Failures:** The speaker demonstrated identifying a "silent failure" where an agent failed to switch to a secondary instruction set because the associated tool wasn't defined in the configuration. By inspecting the `Instruction` node, the developer confirmed that the tool required for the hand-off was missing, allowing for a quick code fix.
*   **Performance Optimization:** Using the instrument’s inspector to view token usage and duration visualizations to identify bottlenecks in the request-response loop.

---

## Practical Takeaways for Developers
*   **Use the Inspector for "Why":** When a model acts unexpectedly, don't just look at the final output. Use the Tree view in Instruments to inspect the specific `Model Inference` node to see exactly what instructions and context the model was provided at that moment.
*   **Verify Tool Configurations:** Always cross-reference your `Dynamic Instructions` definitions with your available `Tool` set. A common cause of stuck loops is failing to explicitly include a required tool in the instruction set, even if it is mentioned in the prompt.
*   **Monitor Instruction Handoffs:** In complex apps, verify the `Instructions` track in the timeline to ensure the app transitions between instruction sets (e.g., from "Brainstorming" to "Tutorial Generation") exactly when intended.
*   **Performance First:** Use the token usage metrics in the inspector as your baseline for optimization. If an experience feels slow, use the `Time to First Token` metric to decide whether to optimize the prompt length or switch models.
*   **Further Learning:** The session suggests pairing the Foundation Models instrument with the **Evaluations framework** to move beyond debugging and start measuring the qualitative success of your AI-driven features.

*Note: To use these features, ensure you have installed Xcode 27 and updated your target devices to the latest OS releases.*

<!-- resources -->

---

## Resources

- [Analyzing the runtime performance of your Foundation Models app](https://developer.apple.com/documentation/FoundationModels/analyzing-the-runtime-performance-of-your-foundation-models-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/243/4/127c397a-8124-4f3d-ad18-ac2a1d275803/downloads/wwdc2026-243_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/243/4/127c397a-8124-4f3d-ad18-ac2a1d275803/downloads/wwdc2026-243_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/243/
