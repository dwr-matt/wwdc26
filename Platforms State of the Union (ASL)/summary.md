# Platforms State of the Union (ASL)

# WWDC 2026: Platforms State of the Union Summary

The 2026 Platforms State of the Union highlights Apple’s vision for an "intelligent platform," emphasizing the deep integration of generative AI into the developer ecosystem. The session centers on three pillars: **Apple Intelligence** (for in-app and system-wide AI), **Platform Improvements** (design, SwiftUI, and performance), and **Developer Productivity** (agentic coding in Xcode 27). The core message is that Apple’s latest tools—built on Swift and the new Liquid Glass design language—enable developers to build smarter, more responsive, and highly personalized applications with less boilerplate.

---

### Key Concepts, APIs, and Frameworks

*   **Apple Intelligence & Models:**
    *   **Foundation Models Framework:** A native Swift API for calling Apple Foundation models (on-device or via Private Cloud Compute). It now supports multimodal prompts (text + images), server-side model integration (Claude, Gemini), and no-cost usage for smaller developers.
    *   **Core AI:** A new framework for running custom, on-device models. It provides memory-safe Swift APIs, custom GPU kernels, and deep integration with the new Xcode toolchain for performance-critical ML.
    *   **App Intents & Schemas:** The bridge to system-wide intelligence. Using Entity and Intent Schemas, apps can expose their data to the Spotlight Semantic Index and allow Siri to interact with app features naturally.
    *   **Dynamic Profiles:** A declarative API in the Foundation Models framework that allows developers to swap tools, instructions, and models on the fly within a single conversational session.

*   **Platform & Design:**
    *   **Liquid Glass Refinements:** Updates to the design language include better diffusion, darkened edges, and user-adjustable transparency settings.
    *   **SwiftUI Enhancements:** New interactions (reorderable containers, swipe actions), improved performance (lazy `StateObject` macros, HTTP-cached `AsyncImage`), and robust document-based infrastructure.
    *   **Spatial Preview Framework:** Enables Mac apps to extend 3D previews into the Apple Vision Pro space.

*   **Tools & Toolchains:**
    *   **Xcode 27:** Features a 30% smaller footprint (Apple Silicon only), iCloud-synced settings, a new project-less startup experience, and personalized themes (e.g., Emerald, Neon Noir).
    *   **Device Hub:** Replaces the legacy Simulator, combining virtual device testing with physical device interaction (iPhone Mirroring).
    *   **Agent Client Protocol (ACP):** Allows any compatible AI agent to plug directly into Xcode, enabling end-to-end coding automation.

---

### Code Patterns & Techniques

*   **Modular AI Sessions:** The session demonstrated using `Dynamic Profiles` to swap between "Brainstorming," "Tutorial Generation," and "Jargon Explanation" models within the same chat thread, maintaining context while optimizing for cost (using on-device models for small tasks and cloud models for reasoning).
*   **View Annotations:** Developers can now annotate views to associate them with App Entities, allowing users to reference on-screen content (e.g., "Send *this* photo") when interacting with Siri.
*   **Swift Interoperability:** Continued progress in C++ interoperability allows WebKit and other low-level systems to incrementally migrate to Swift, enhancing memory safety without full re-writes.
*   **Agentic Coding:** Developers are encouraged to use `/plan` with coding agents. Agents can now access Xcode tools to analyze codebases, run tests, and debug crashes, grounded in Apple’s framework-specific documentation.

---

### Practical Takeaways for Developers

1.  **Immediate AI Adoption:** Smaller developers (under 2M first-time downloads) can access Private Cloud Compute for Apple Foundation models without infrastructure costs.
2.  **System Discovery:** Adopt `App Intents` immediately. By mapping your app’s actions to system schemas, your app becomes part of the "intelligent fabric" of iOS/macOS, allowing Siri to perform complex tasks within your app without custom phrase training.
3.  **Future-Proofing Layouts:** Design for dynamic aspect ratios rather than fixed devices. Use the new resizable simulator to ensure your UI remains robust as users move between iPad, Mac, and mirrored iPhone views.
4.  **Xcode Productivity:** Shift your workflow to leverage the Agent Client Protocol. Use the new Device Hub for high-fidelity testing of both physical and simulated hardware.
5.  **Performance Wins:** If you are using SwiftUI, you get many performance gains (e.g., lazy state initialization and better toolbar handling) simply by recompiling with the new SDK.
6.  **Open Source & Community:** Apple is increasingly moving core tools to open source (e.g., Foundation Models framework, Swift networking stacks), encouraging a cross-platform (Linux/Windows/Server) approach to Swift.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/112/1/0e1d49e8-277b-49f9-aaff-d937c5956d86/downloads/wwdc2026-112_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/112/1/0e1d49e8-277b-49f9-aaff-d937c5956d86/downloads/wwdc2026-112_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/112/
