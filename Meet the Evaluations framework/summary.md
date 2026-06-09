# Meet the Evaluations framework

# Session Summary: Meet the Evaluations Framework (WWDC 2026)

## Overview
This session introduces the **Evaluations Framework**, a new Apple developer tool designed to bring scientific rigor to the testing of "intelligent features" powered by generative AI and other stochastic models. Because AI models are probabilistic (the same input can yield different outputs), traditional unit tests are insufficient. The Evaluations Framework provides a structured way to measure model quality, track improvements over time, and ensure that intelligent features are safe, reliable, and trustworthy.

---

## Key Concepts, APIs, and Frameworks

### Core Philosophy
*   **Evaluation-Driven Development:** A development cycle centered on "hill climbing"—running evaluations, analyzing results, refining model instructions/prompts, and repeating until performance targets are met.
*   **Stochastic Testing:** Moving away from static inputs/outputs toward testing patterns, trends, and distributions.
*   **Quantitative vs. Qualitative:**
    *   **Quantitative Metrics:** Objective, rule-based measurements (e.g., "does the array have 5 items?").
    *   **Qualitative Metrics:** Subjective measurements requiring an intelligent judge (e.g., "is this tag helpful for browsing?").

### Primary Framework Components
*   **`Evaluation` Protocol:** The primary interface for defining test suites.
*   **`Evaluator` & `Metric`:** Types used to define how an output is measured.
*   **`ModelSample`:** A wrapper for input data, expected results, and test parameters.
*   **`ModelJudge`:** A specialized evaluator that uses a more capable language model (often running on Private Cloud Compute) to score the output of a local on-device model.
*   **Swift Testing Integration:** Evaluations integrate directly into Xcode’s test targets using the `@Test` macro and a new `evaluates` trait.

---

## Code Patterns & Techniques

### The Five Steps of an Evaluation
1.  **Subject:** Define the code path/model being measured.
2.  **Sample Data:** Define the input cases and expected outcomes.
3.  **Measurements:** Create metrics (using `Evaluator`) to check individual samples.
4.  **Summarize:** Aggregate metrics across the entire dataset.
5.  **Test Execution:** Run the suite within an Xcode test target and assert against aggregate results.

### Hill Climbing & Refinement
*   **Synthetic Data Generation:** Use the framework’s built-in sample generator to expand small test datasets into thousands of varied examples.
*   **Splitting Dimensions:** If a model judge produces vague results, developers should split broad metrics (e.g., "Quality") into specific "Score Dimensions" (e.g., "Relevance" and "Usefulness").
*   **Contextual Prompting:** When using a Model Judge, provide specific system instructions (e.g., "You are evaluating tags for a personal library app") so the judge understands the app's specific domain context.

---

## Practical Takeaways for Developers

*   **Start Small:** Begin with 20–30 hand-curated samples to establish a baseline before scaling to thousands of synthetic samples.
*   **The "Rule of Thumb":**
    *   If you can check it with code (regex, count, type), use a **Quantitative Metric**.
    *   If you can only describe it with words, use a **Model Judge (Qualitative Metric)**.
*   **Use Rationales:** Always require Model Judges to provide a "rationale" for their score. This acts as a diagnostic tool, allowing developers to see *why* a model failed, which is critical for refining prompts.
*   **Leverage Xcode Reports:** The new Evaluation test report provides a visual dashboard to compare performance across different runs, making it easy to track whether an update to a prompt actually improved the feature or introduced regressions.
*   **Don't rely on Neutrality:** Use an even-numbered scale (e.g., 1–4) for Model Judge scoring to prevent the judge from defaulting to a "neutral" middle value.

### Suggested Follow-up Resources
*   *Create Robust Evaluations for Agentic Apps* (Advanced use of `ModelSample`).
*   *Improve Your Prompts by Hill Climbing with Evaluations* (Deep dive into prompt iteration strategies).

<!-- resources -->

---

## Resources

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Designing datasets to test your feature](https://developer.apple.com/documentation/Evaluations/designing-evaluation-datasets)
- [Designing effective evaluations](https://developer.apple.com/documentation/Evaluations/designing-effective-evaluations)
- [Evaluating language model responses](https://developer.apple.com/documentation/Evaluations/evaluating-language-model-responses)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/298/5/0ffb7161-1edb-4e6f-872d-55be82c4402d/downloads/wwdc2026-298_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/298/5/0ffb7161-1edb-4e6f-872d-55be82c4402d/downloads/wwdc2026-298_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/298/
