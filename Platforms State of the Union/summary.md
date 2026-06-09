# Platforms State of the Union

# WWDC 2026: Platforms State of the Union Summary

The 2026 Platform State of the Union outlines Apple’s vision for the next generation of application development, anchored by a deep integration of **Apple Intelligence**, the **Liquid Glass** design language, and significant advancements in **agentic coding** tools. The session focuses on empowering developers to build more intelligent, adaptive, and responsive apps by providing native frameworks for AI, modernizing the UI layer with SwiftUI, and transforming the developer experience in Xcode 27.

---

### Key Frameworks & Technologies

#### 1. Artificial Intelligence
*   **Foundation Models Framework:** Now supports multimodal prompts (text and images), vision framework integration (OCR/barcode scanning), and server-side model calling (Claude, Gemini, etc.). It features **Dynamic Profiles** for declarative, adaptive AI orchestration, allowing developers to swap models, tools, and instructions on-the-fly.
*   **Core AI:** A new framework for high-performance, on-device model execution. It offers fine-grained inference management, custom GPU kernels, and a modern Swift API, allowing for anything from compact vision models to multi-billion parameter LLMs on Mac.
*   **App Intents Framework:** The bridge between app content and the system. It uses **Entity Schemas** and **Intent Schemas** to allow Siri to "understand" and act upon specific in-app content.
*   **MLX:** An open-source array framework now supporting Metal 4, GPU neural accelerators, and distributed training over Thunderbolt.

#### 2. Design & Platform Improvements
*   **Liquid Glass Refinements:** Updates include improved diffusion for readability, new darkened edges for depth, and per-item tinting in sidebars. These apply automatically to apps recompiled with the new SDK.
*   **Adaptive Layouts:** iOS apps running via iPhone mirroring or on iPad can now be resized dynamically. Developers are encouraged to move away from device-specific designs to a fluid layout model using Auto Layout and trait collections.

#### 3. Swift & SwiftUI
*   **Swift 6.4:** Focuses on developer workflow, including warning suppression, improved compiler diagnostics, and the ability to use `await` in `defer` blocks.
*   **SwiftUI Enhancements:**
    *   **Performance:** Faster layouts via "short-circuiting" computations and lazy state object initialization.
    *   **Interactions:** Native support for drag-to-reorder in grids/stacks and expanded swipe-action capabilities.
    *   **Document Infrastructure:** New APIs providing first-class URL access for partial file reads/writes, improving performance for document-based apps.

---

### Code Patterns & Techniques Demonstrated

*   **Dynamic Profiles:** Using a result-builder-like syntax to define different "modes" for an AI agent within a single session. This allows the app to switch from a "Brainstorming" mode to a "Tutorial" mode seamlessly, maintaining context throughout.
*   **View Annotations:** Developers can associate specific views with App Entities, allowing users to reference objects (like "this photo" or "this message") in natural language conversations with Siri.
*   **Toolbar Prioritization:** Using new visibility modifiers to control which buttons stay visible during window resizing and which are relegated to an automatic overflow menu.
*   **Agentic Coding:** Xcode 27 integrates coding agents that use the **Model Context Protocol (MCP)**. Developers can provide a "plan" (using `/plan` in the chat) to let the agent generate code, build/test, and even perform debugging iterations end-to-end.

---

### Practical Takeaways for Developers

*   **Adopt SwiftUI for Performance:** The move toward unifying the architecture of SwiftUI, AppKit, and UIKit means that standard SwiftUI components are receiving significant "for-free" performance boosts.
*   **Intelligence Strategy:**
    *   Use **Foundation Models Framework** for general-purpose AI tasks that need to be simple and portable.
    *   Use **Core AI** when you have custom, specialized, or performance-critical models that must run on-device.
    *   Use **App Intents** to ensure your app is discoverable and actionable by system-wide AI (Siri).
*   **Prepare for Resizability:** Because iOS apps now show up on larger displays via iPhone mirroring and resizing, review your layouts to ensure they handle dynamic aspect ratios. Xcode 27’s new **Device Hub** and resizable simulators are the primary tools for testing these variants.
*   **Leverage Xcode 27:**
    *   **Settings Sync:** Settings and Git configs are now saved to iCloud.
    *   **Agentic Workflow:** Treat coding agents as "specialists" (e.g., UI, Accessibility, Testing). Integrate them early in the development lifecycle to handle localization, unit testing, and crash resolution.
    *   **Themes:** Xcode now supports global theme customization (e.g., Emerald, Neon Noir) to help differentiate projects visually.
*   **Open Source Commitment:** The Foundation Models Framework will be open-sourced later this summer, enabling end-to-end AI workflows for Swift on both the client and the server.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/102/2/abb4bd38-dfae-46cf-985f-160769b92d41/downloads/wwdc2026-102_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/102/2/abb4bd38-dfae-46cf-985f-160769b92d41/downloads/wwdc2026-102_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/102/
