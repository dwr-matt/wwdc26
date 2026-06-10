# Speedrun your game port with agentic coding

# Session Summary: Speedrun your game port with agentic coding

## Overview
This session introduces the **Game Porting Toolkit 4 (GPTK 4)**, a significant evolution in Apple’s developer tooling that shifts the porting paradigm from manual implementation to an **agentic coding workflow**. By leveraging AI-driven "Expert Skills" and a centralized "Porting Assistant," developers can now automate the tedious aspects of porting game engines (e.g., D3D12 to Metal) while maintaining high architectural quality. The session demonstrates that this approach allows for faster development cycles, systematic debugging, and professional-grade performance optimization for Apple platforms.

---

## Key Concepts, APIs, and Frameworks

*   **Porting Assistant Agent:** The orchestrator of the development lifecycle. It breaks down large ports into manageable, milestone-based tasks, maintains state between sessions, and ensures the correct skills are loaded at the right time.
*   **Expert Skills:** Modular, domain-specific plugins (available via GitHub) that provide the agent with platform-specific knowledge, such as Metal 4 API requirements, Apple Silicon memory management, and best practices.
*   **Metal 4:** The latest iteration of Apple’s graphics API, emphasizing explicit memory management and modern command structures.
*   **New macOS 27 Debugging Tools:**
    *   **GPU Capture:** Autonomous frame capture for analyzing rendering traces.
    *   **GPU Debug:** A command-line tool allowing the agent to programmatically inspect resource bindings, constants, and data flow.
*   **Metal FX:** Frameworks for upscaling and frame interpolation, now integrated with agent-guided workflows to handle complex tasks like motion vector calculation and jitter sequences.

---

## Demonstrated Code Patterns & Techniques

*   **Structured Workflow:** The session outlines a three-stage porting process:
    1.  **Discovery:** Analyzing the existing codebase and reference captures.
    2.  **Milestone Execution:** Iterative, agent-led implementation guided by expert skills.
    3.  **Validation:** Multi-point checklist validation, including Metal API validation, visual comparison against ground truth, and memory leak checks.
*   **Residency Sets:** Using expert skills to teach the agent to register resources in a residency set *before* use, ensuring GPU accessibility—a common point of failure for manual ports.
*   **Handling Synchronization:** Replacing "blanket barriers" with explicit, granular mapping between D3D12 state transitions and Metal 4’s producer-consumer synchronization model.
*   **Metal FX Tuning:** Using the **Metal HUD** to perform real-time overrides of motion vector scales and jitter multipliers, allowing developers to visually diagnose and fix artifacts (e.g., "wobbling" or blurriness) caused by incorrect integration logic.
*   **Input Handling:** Moving from hard-coded controller mapping (e.g., XInput) to dynamic, discovery-based patterns that handle device connection/disconnection events via the `GCController` API.

---

## Practical Takeaways for Developers

*   **Focus on Architecture, Not Boilerplate:** The AI agent handles the platform-specific "heavy lifting" (API translation, shader mapping, memory layout). The developer should act as an architect—providing context, making high-level design decisions, and performing final reviews.
*   **Leverage Evaluation Environments:** Always maintain a "ground truth" environment (e.g., the original Windows build) to provide the agent with reference data for validation.
*   **Use the Marketplace:** The Game Porting Toolkit 4 plugins are distributed via the **Game Porting Toolkit Marketplace on GitHub**. Installing these is the first step toward enabling the agentic workflow.
*   **Don't Guess—Debug:** Instead of manually digging through frame traces, use the new GPU Debug tools. If the agent makes a mistake, use the HUD to override parameters in real-time; once the fix is verified in the HUD, trace those changes back to the source code.
*   **Scalability:** The workflow is not limited to simple projects. The demonstration showed that these skills can be successfully applied to production-grade engines like **Godot**, enabling the addition of new graphics backends (e.g., adding Metal 4 to an existing Metal 3 setup) in a matter of days.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/357/5/5cfd0ceb-598f-4535-9abc-12e22a778326/downloads/wwdc2026-357_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/357/5/5cfd0ceb-598f-4535-9abc-12e22a778326/downloads/wwdc2026-357_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/357/
