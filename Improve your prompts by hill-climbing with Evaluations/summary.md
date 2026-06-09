# Improve your prompts by hill-climbing with Evaluations

# WWDC 2026: Improve your prompts by hill-climbing with Evaluations

## Overview
This session introduces the concept of **"hill-climbing"** within the Apple Evaluations framework—a systematic, iterative process for refining intelligence-powered features. By treating development as a series of controlled scientific experiments, developers can systematically improve AI prompts and feature behaviors. The session emphasizes the necessity of aligning automated "model judges" with human expert standards to avoid "drift," ensuring that evaluation scores remain trustworthy as datasets scale.

---

## Key Concepts, APIs, and Frameworks
*   **Hill-Climbing:** An iterative optimization process where developers make incremental changes to their app (e.g., prompt updates, tool additions) and use evaluation scores as a feedback loop to guide further improvements.
*   **Evaluations Framework:** Apple’s suite of tools for measuring the quality of AI-driven features. It allows for defining datasets, subject evaluators, and aggregation metrics.
*   **Drift:** The phenomenon where the ratings provided by an automated "model judge" begin to diverge from human expert ratings over time.
*   **Cohen’s Kappa Coefficient:** A statistical metric used to measure the alignment (agreement) between the human rater and the model judge, accounting for the possibility of random chance agreement.
*   **Swift Testing:** The session utilizes `expect` macros to define success criteria for evaluation runs, allowing developers to programmatically validate if improvements meet specific thresholds.

---

## Code Patterns and Techniques
*   **Comparative Evaluation:** The session demonstrates running two versions of a prompt—a "control" (baseline) and an "experimental" (new) version—within the same test suite to measure the impact of specific changes.
*   **Alignment Evaluation:** Developers can build a dedicated test to compare a human's expert ratings against the model's automated scores. The formula for alignment involves:
    1.  **Accuracy:** Percentage of matching scores.
    2.  **Coincidence:** Probability of random agreement.
    3.  **Kappa:** `(Accuracy - Coincidence) / (1 - Coincidence)`.
*   **Iterative Prompt Engineering:** Instead of broad changes, the session demonstrates:
    *   Adding context (app purpose) to prompts.
    *   Adding positive/negative examples (few-shot prompting) to clarify scoring dimensions.
    *   Refining scoring rubrics (e.g., strictly defining what constitutes a "useful" vs. "irrelevant" tag).
*   **Feature-Level Improvements:** Beyond prompts, developers can "hill-climb" by adding tools (e.g., fetching external metadata like author names) and comparing feature performance with and without those tool calls enabled.

---

## Practical Takeaways for Developers
*   **Isolate Variables:** Only change one variable at a time (e.g., a specific prompt instruction or a single tool) to ensure that evaluation results are attributable to that specific change.
*   **Evaluate the Evaluator:** Don’t blindly trust your model judge. Periodically calculate alignment (using Cohen’s Kappa) to ensure the judge is still representing your professional standards.
*   **Fail Fast and Learn:** Failed experiments are as valuable as successful ones. If a prompt change reduces a usefulness score while increasing a relevance score, it provides a clear roadmap for where to focus the next round of engineering.
*   **Beware of Overfitting:** When providing examples to your model judge, do not provide an exhaustive list, as this can lead to overfitting the model to your specific dataset and failing to generalize across a wider range of user inputs.
*   **Use the Comparison Report:** Xcode’s evaluation report provides a side-by-side view to compare baseline results with experimental results, which is essential for identifying exactly why a score increased or decreased.
*   **Next Steps:** For more complex scenarios, investigate **Tool Call Evaluators** and the **Sample Generator API** (as introduced in the companion session, *Create Robust Evaluations for Agentic Apps*).

<!-- resources -->

---

## Resources

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Designing effective model-as-judge evaluators](https://developer.apple.com/documentation/Evaluations/designing-effective-model-judges)
- [Designing specific, measurable criteria in an evaluation suite](https://developer.apple.com/documentation/Evaluations/designing-evaluation-criteria)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/335/4/a464d330-6aa2-456d-9a07-eae997aef08c/downloads/wwdc2026-335_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/335/4/a464d330-6aa2-456d-9a07-eae997aef08c/downloads/wwdc2026-335_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/335/
