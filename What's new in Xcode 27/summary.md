# What's new in Xcode 27

# WWDC 2026: What’s New in Xcode 27

## Overview
This session introduces the major feature set of Xcode 27, focusing on an improved developer experience through a highly customizable workspace, deeper integration with AI-powered coding agents, and enhanced diagnostic tools. The presenters demonstrate how Xcode 27 supports the full app lifecycle—from rapid prototyping and AI-assisted development to localization, performance profiling, and automated CI/CD workflows using Xcode Cloud.

---

## Key Concepts & Features

### 1. Customizable Workspace
*   **Revamped Toolbar:** Controls (history navigation, editor controls) have moved to the main toolbar. The layout is now fully modular, allowing developers to reorder or remove items.
*   **Dynamic Themes:** The Appearance panel now features intensity sliders for text and backgrounds. Themes can support vibrant gradients, and unique themes can be assigned per workspace to help differentiate multiple open projects.
*   **Inline Issue Feedback:** A new "subtle" visual style for predictive/live issues distinguishes them from actual build errors, reducing visual noise while typing.

### 2. AI Coding Agents
*   **Agentic Workflow:** Coding agents are now integrated directly into the editor pane. The transcript and conversation history move with the editor, supporting splits, tabs, and multi-pane views.
*   **Planning Tool:** A new `/plan` command allows developers to outline a task before execution. The agent gathers context, iterates with sub-agents in parallel, and presents a plan for user approval before modifying code.
*   **Artifact Visualization:** Changes, generated code, and UI screenshots appear in a side-by-side view to monitor agent progress in real time.

### 3. DeviceHub
*   **Unified Device Management:** A new hub for interacting with simulators and physical devices. It provides compact controls (rotation, screenshot, Home) and an inspector for toggling accessibility settings like Dynamic Type, Dark Mode, and high-contrast settings without leaving Xcode.
*   **Live Resizing:** Supports iPhone mirroring with dynamic aspect ratios and content size testing.

### 4. Localization
*   **Agent-Assisted Translation:** Developers can ask the agent to localize an entire project. It identifies localizable strings, creates a String Catalog, and generates translations using project-specific style guidance.
*   **Streamlined Catalog:** New "Generate Translations" button in the String Catalog allows for batch processing of new languages (e.g., Simplified Chinese).

### 5. Organizer & Performance
*   **Metric Goals:** The Organizer now provides performance goals (Hang rate, Disk writes, Battery, Storage, and Animation Hitches) based on technical benchmarks from similar apps.
*   **Guided Analysis:** Agents can now generate recommended fixes for performance issues discovered in the Organizer.
*   **Instruments Improvements:** The new **"Top Functions"** view helps developers identify expensive operations by surfacing the most time-consuming functions in the CPU profile, directly linking to the problematic code.

---

## Code Patterns & Techniques
*   **Rapid Prototyping:** Developers can create "untitled" projects for experimentation without immediate file system save requirements.
*   **Quick Open:** Using `Command-Shift-O` now allows searching for specific function names within the codebase to jump directly to performance-critical code identified in Instruments.
*   **Regression Testing:** Setting up automatic unit and UI tests in Xcode Cloud is now a "Get Started" workflow integrated into the project settings, triggering on every commit to ensure stability.

---

## Practical Takeaways for Developers
*   **Workflow Optimization:** Use project-specific themes to manage cognitive load when working on multiple projects simultaneously.
*   **Performance First:** Shift performance profiling "left" by using the Organizer’s new metrics and recommendations to address regressions (like animation hitches) before shipping.
*   **Leverage AI for Boilerplate:** Offload repetitive tasks—such as updating String Catalogs or initial localization—to the coding agent, keeping the developer in the role of editor/reviewer.
*   **Refine Instruments Usage:** Focus on the new "Top Functions" view to debug expensive loops or animation pipelines rather than manually digging through call trees.
*   **Automate CI:** Implement Xcode Cloud early in the development cycle to catch regressions during branch integration, rather than manual validation.

---

### Recommended Sessions for Further Learning
*   *Xcode Agents and You*
*   *Get the Most out of Device Hub*
*   *Translate Your App Using Agents in Xcode*
*   *Debug and Profile Agentic App Experiences with Instruments*
*   *Build, Deliver, and Automate with Xcode Cloud*

<!-- resources -->

---

## Resources

- [Xcode updates](https://developer.apple.com/documentation/Updates/Xcode)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/258/4/66bc9c90-649b-4a16-a2bb-1e6f16b1ec73/downloads/wwdc2026-258_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/258/4/66bc9c90-649b-4a16-a2bb-1e6f16b1ec73/downloads/wwdc2026-258_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/258/
