# Xcode, agents, and you

# WWDC 2026 Session Summary: Xcode, Agents, and You

## Overview
This session introduces the advanced agentic workflow capabilities in **Xcode 27**, building upon the foundational coding agents introduced in Xcode 26.3. The presenters demonstrate how developers can leverage AI agents to manage the entire application development lifecycle—from project exploration and architectural planning to UI refinement and complex task orchestration. The session emphasizes a "human-in-the-loop" philosophy, where the agent acts as an assistant that executes technical tasks while the developer remains the lead architect.

## Key Concepts and Tools
*   **Agent Exploration:** Agents can analyze entire codebases, including build settings and source files, to provide architectural walkthroughs. These can be saved as **project artifacts** to create a living, dynamic knowledge base.
*   **Plan Mode:** A specialized mode where developers outline their architectural strategy via text before any code is generated. This ensures the foundation is solid before implementation begins.
*   **Queued Messaging:** Developers can send follow-up instructions to an agent while it is still processing a current task, creating a more fluid, conversational, and interactive workflow.
*   **Artifacts:** The Xcode UI now distinguishes between the **Transcript** (the conversation/logic) and **Artifacts** (code diffs, rendered previews, documentation, and architecture diagrams).
*   **Orchestration:** The ability to assign high-level, complex goals (like "localize the entire app") which the main agent then breaks down into smaller sub-tasks, deploying specialized sub-agents to execute work in parallel.
*   **Apple Document Search:** Agents are natively integrated with official Apple documentation, ensuring that code generated or suggested aligns with the latest framework best practices.

## Demonstrated Techniques
*   **Visual Refinement:** Developers can upload sketches or screenshots to the agent to dictate UI design. The agent translates these visual inputs into SwiftUI code.
*   **Inline Annotations:** Instead of broad descriptions, developers can point to specific lines of code in the editor to provide targeted instructions, such as "add a fade-in animation here" or "change the trendline color to match the theme."
*   **Self-Validation Loop:** Agents are integrated with Xcode’s internal tools to verify their own work. This includes:
    *   **Build tools:** Identifying and fixing compilation errors automatically.
    *   **Previews:** Rendering the UI to verify it matches the user's intent.
    *   **Testing:** Writing and executing unit tests to confirm model changes function as expected.
*   **Incremental Previews:** When creating UI elements like `Swift Charts`, the agent renders previews in real-time to allow for rapid visual iteration before the developer finalizes the code.

## Practical Takeaways for Developers
1.  **Stop "Cold Starting":** Use the agent’s exploration walkthroughs to generate architecture documentation for new projects immediately, saving time on codebase familiarity.
2.  **Architect Before You Code:** Use "Plan Mode" to enforce a strategy-first workflow. This prevents the "spaghetti code" that can occur when agents are asked to write complex features without a clear roadmap.
3.  **Leverage Multimodal Input:** Don't rely solely on text. Use image attachments (sketches) and inline code annotations to give the agent precise, high-fidelity context.
4.  **Automate Non-Functional Requirements:** Treat "big" tasks like Localization and Accessibility as orchestration workflows. Simply defining the goal allows the agent to handle the mechanical, repetitive nature of these tasks.
5.  **Maintain Confidence through Artifacts:** Always review the generated "Artifacts" (diffs and previews) provided by the agent. Xcode is designed to keep the developer in the driver's seat by providing transparency into every change the agent makes.

***

**Suggested Next Steps:**
*   Download **Xcode 27**.
*   View the companion sessions: *Create UI Prototypes Using Agents in Xcode* and *Translate Your App Using Agents in Xcode* for deeper technical implementations.

<!-- resources -->

---

## Resources

- [Writing code with intelligence in Xcode](https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/259/4/f4d40bb5-32db-418f-8a6e-396c77044afb/downloads/wwdc2026-259_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/259/4/f4d40bb5-32db-418f-8a6e-396c77044afb/downloads/wwdc2026-259_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/259/
