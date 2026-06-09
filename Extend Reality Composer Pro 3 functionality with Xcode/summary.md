# Extend Reality Composer Pro 3 functionality with Xcode

## Session Summary: Extend Reality Composer Pro 3 functionality with Xcode

### Overview
This session introduces the plugin architecture for **Reality Composer Pro 3**, enabling developers to extend the editor’s capabilities using Xcode. By creating custom plugins, developers can expose product-specific content, logic, and systems directly to artists and designers within the editor. This bridge between the Xcode project and the Reality Composer Pro 3 interface allows for real-time iteration, where code-driven behaviors (such as custom components, shaders, and animations) can be tested and tweaked visually without requiring a full app rebuild.

---

### Key Concepts, APIs, and Frameworks
*   **Plugin Architecture:** The core mechanism for extending the editor. Plugins are built as dynamic libraries within an Xcode project and loaded by Reality Composer Pro 3 to provide custom editor functionality.
*   **Reality Composite Pro Swift Package:** Provides the necessary protocols to integrate custom components and systems into the editor.
*   **RealityKit Scripting & Macros:** New `@scriptable` macros allow developers to automatically generate schemas for custom components, making them instantly available as nodes within the visual Script Graph.
*   **Reality File Format:** The serialization format for RealityKit. Data created in the editor is converted to this format to be loaded by the final application.
*   **Git Integration:** Enhanced support for managing RCP projects, including a custom merge tool designed to minimize conflicts when working in team-based environments.

---

### Demonstrated Code Patterns & Techniques
*   **Plugin Registration:** Developers implement the `RealityComposerProPlugin` protocol. The registration process requires an exported C-function (`CreateRealityComposerProPlugin`) to return a raw pointer to the plugin, allowing the editor to load the dynamic library.
*   **Custom Components & Systems:** 
    *   Components must implement `Codable` to be serialized in the editor.
    *   Systems can be registered to the editor context to run in real-time, allowing developers to manipulate mesh positions, vertex shaders, or material parameters based on component properties.
*   **Custom Animation Actions:** By implementing the `EntityAction` protocol and using the `subscribe` method, developers can create custom animation clips that appear in the **Sequencer Timeline**. This allows for interpolation of custom data types over time within the editor.
*   **Script Graph Integration:** The `@scriptable` macro on a `Component` struct, combined with `RealityKitScripting` configuration, bridges C-style code properties into user-friendly nodes for visual scripting.

---

### Practical Takeaways for Developers
*   **Unified Development Workflow:** By sharing a single Git repository between an Xcode project and a Reality Composer Pro project, teams can maintain a "single source of truth" for code and content.
*   **Interactive Debugging:** Developers can attach the Xcode debugger to the Reality Composer Pro 3 process, allowing them to set breakpoints and inspect logic while the editor is running.
*   **Designer Empowerment:** By exposing properties (like "water level" or "vortex rotation") through custom components, you empower artists to handle fine-tuning and visual effects independently, reducing the bottleneck on engineering resources.
*   **Plugin Management:**
    *   Plugins are managed via the simulation toolbar. 
    *   The editor includes a "Trust" mechanism for security when loading custom plugins.
    *   Rebuilding a plugin typically requires a restart of the Reality Composer Pro 3 editor to refresh the loaded dynamic library.
*   **Best Practices:**
    *   Use `Script Graph` for simple logic, but transition to **custom components/systems** when dealing with complex state management, performance-heavy logic, or integrations with other Apple frameworks (e.g., SwiftUI).
    *   Always verify that components implementing `Codable` are correctly mapped for serialization to ensure data persists correctly across the editor-to-app pipeline.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/281/6/1aef704f-ccc6-4c1d-b7b7-94da42d29609/downloads/wwdc2026-281_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/281/6/1aef704f-ccc6-4c1d-b7b7-94da42d29609/downloads/wwdc2026-281_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/281/
