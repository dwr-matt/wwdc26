# Create robust evaluations for agentic apps

# WWDC 2026: Create robust evaluations for agentic apps

## Overview
This session introduces advanced capabilities within the Xcode **Evaluations Framework** (introduced in Xcode 27), focusing on scaling the testing of intelligence-powered features. As developers move from prototyping to production, small manual datasets often fail to capture real-world complexity. The session provides two primary solutions: **Synthetic Data Generation** to expand evaluation coverage, and **Tool Evaluations** to ensure the "how" (process) of an agentic workflow is as correct as the "what" (output).

---

## Key Concepts, APIs, and Frameworks

### 1. Synthetic Data Generation
*   **Purpose:** To overcome the limitations of small, hand-crafted datasets and capture the variety of user inputs and edge cases.
*   **`Make Samples` API:** The primary entry point for generating new samples. It requires a `prompt`, an initial `dataset`, and a `targetCount`.
*   **`SampleGenerator`:** Provides full control over the generation pipeline, allowing for custom `sessionProvider` configurations (e.g., using Private Cloud Compute) and complex validation logic.
*   **Sampling Strategies:**
    *   **Random Sampling:** Selects a random subset of initial data to provide context to the model, ensuring variety.
    *   **Sliding Window:** Steps through initial samples sequentially; ideal for datasets with a logical or meaningful order.

### 2. Tool Evaluations
*   **Purpose:** To verify that an AI agent is using tools correctly by checking the "trajectory" of the model's logic.
*   **Trajectory Expectation:** A check on the model's transcript that verifies the sequence, presence, or absence of specific tool calls.
*   **Matchers:** Specialized logic used to validate tool calls, such as `exact match`, `contains`, `one of`, `pattern`, and `range`. Crucially, **Natural Language Matchers** allow for semantic matches (e.g., "happy" vs. "cheerful") rather than strict string comparisons.
*   **`ToolCallEvaluator`:** The component that combines the model session, defined tools, and trajectory expectations to provide a final score.

---

## Code Patterns and Techniques

*   **Custom Session Configuration:** Developers can define a `sessionProvider` to set custom system instructions for the model, specify larger context windows, or switch between on-device and cloud-based models.
*   **Validation Closures:** Developers can define logic to accept or reject generated samples (e.g., checking for specific string lengths, number of tags, or formatting rules). Invalid samples are automatically sequestered by the framework.
*   **Ensuring Context Stability:** Since sessions might be recreated if context limits are reached during large generation runs, system instructions must be self-contained and stateless.
*   **Disallowed Tools:** A key parameter in trajectory expectations that ensures the agent does not call specific tools when prohibited, which is vital for safety and logic flow.

---

## Practical Takeaways for Developers

*   **Focus on Coverage over Quantity:** Don't just increase the sample count; focus on whether the dataset covers the variety of ways your feature will be used in the real world.
*   **Treat Evaluations as an Iterative Process:** The lifecycle of building agentic features involves: *Define initial samples -> Generate synthetic data -> Validate samples -> Run evaluations -> Analyze score drops.*
*   **Understand the "Why" behind Score Drops:** If scores drop when increasing dataset size, do not view it as a failure. It is an honest signal that your previous, smaller dataset was hiding performance gaps in your prompt, instructions, or feature logic.
*   **Evaluate the Journey, Not Just the Destination:** For agentic apps, a "correct" output achieved through "incorrect" tool calls is still a failure. Use **Trajectory Expectations** to enforce proper tool usage, argument passing, and call ordering.
*   **Synthesize Tool Evaluations:** You can use the `SampleGenerator` to create synthetic data specifically for tool calls by including your tool definitions and order expectations in the prompt/instructions provided to the generator.

<!-- resources -->

---

## Resources

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Generating synthetic datasets](https://developer.apple.com/documentation/Evaluations/generating-synthetic-evaluation-datasets)
- [Evaluating tool-calling behavior](https://developer.apple.com/documentation/Evaluations/evaluating-tool-calling-behavior)
- [Scoring with model-as-judge evaluators](https://developer.apple.com/documentation/Evaluations/scoring-with-model-as-judge-evaluators)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/299/4/ef9fbc06-fc78-4896-9848-0f0fe2e75fb9/downloads/wwdc2026-299_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/299/4/ef9fbc06-fc78-4896-9848-0f0fe2e75fb9/downloads/wwdc2026-299_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/299/
