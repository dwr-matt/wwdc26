# Translate your app using agents in Xcode

# WWDC 2026: Translate your app using agents in Xcode

## Overview
This session introduces the integration of **coding agents** into Xcode 27 to streamline the app localization process. By leveraging the contextual information already gathered by Xcode’s string catalogs (which track code usage and string intent), developers can now automate the translation of their entire project. The session demonstrates how these agents work collaboratively to perform context-aware translations, handle plural variations, and assist in identifying UI layout issues caused by language-specific text length differences.

## Key Concepts, APIs, and Frameworks
*   **String Catalogs:** The backbone of the localization process. Xcode uses these to track where and how strings are used, allowing agents to provide accurate, context-aware translations rather than literal, word-for-word interpretations.
*   **Coding Agents:** AI-driven assistants integrated into the Xcode toolbar that manage the end-to-end translation lifecycle, from preparing the project to managing complex pluralization logic.
*   **Subagents:** The system delegates bulk translation tasks to specialized subagents, which utilize project-wide context (e.g., existing terminology, similar usage patterns) to ensure consistency.
*   **TestFlight:** Emphasized as a crucial tool for getting real-world feedback from native speakers to catch cultural nuances and localization errors before a public release.

## Code Patterns and Techniques
*   **Automated Localization Workflow:**
    1.  **Preparation:** The agent updates project settings to include the target language and forces a build of all targets to discover every localizable string.
    2.  **Catalog Generation:** Xcode automatically creates or updates string catalogs (e.g., `Localizable.xcstrings`) based on the project structure.
    3.  **Context-Aware Translation:** Subagents reference code locations and previous translations to correctly handle ambiguous terms (e.g., "book" as a noun vs. a verb).
    4.  **Pluralization:** The agents automatically implement the correct plural variations for the target language (e.g., handling French "un/deux" variations).
*   **Layout Verification:** Developers can prompt an agent to render the UI in a specific locale (e.g., Canadian French) to visually inspect for text truncation, vertical clipping, or alignment issues (essential for RTL languages like Arabic).
*   **Customization via `translation.md`:** Developers are encouraged to create a `translation.md` file in their project root to provide the agents with:
    *   **Glossaries:** Specific terminology to maintain brand voice.
    *   **Exclusions:** A list of terms (product names/trademarks) that should never be translated.
    *   **Style Guides:** Plain text descriptions of the app's desired tone (e.g., professional for banking, casual for games).

## Practical Takeaways for Developers
*   **Start Localizable:** Ensure your app is built using localization-ready APIs (e.g., SwiftUI `Text` and `Button` views, or `String(localized:)` for older codebases) to ensure the agents can actually "see" the text.
*   **Iterative Design:** Use the agent to check UI previews in various languages early in the development cycle to avoid expensive post-development redesigns for longer strings.
*   **Model Selection:** When performing large-scale translation, use models with larger context windows to ensure consistency across the entire codebase.
*   **Human-in-the-Loop:** While agents handle the heavy lifting, developers remain responsible for quality control. Use native speaker feedback via TestFlight and your own domain knowledge to refine the agent's output.
*   **Leveraged MT:** If exporting localizations, look for the `Leveraged MT` (Machine Translation) qualifier in your files to audit which strings were machine-generated versus human-verified.

<!-- resources -->

---

## Resources

- [Localizing your app using agents](https://developer.apple.com/documentation/Xcode/localizing-your-app-using-agents)
- [Expanding Your App to New Markets](https://developer.apple.com/localization/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/213/4/be1ee662-a447-4df4-89a5-5411447c0eeb/downloads/wwdc2026-213_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/213/4/be1ee662-a447-4df4-89a5-5411447c0eeb/downloads/wwdc2026-213_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/213/
