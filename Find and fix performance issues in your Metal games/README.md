# Find and fix performance issues in your Metal games

# WWDC 2026: Find and Fix Performance Issues in Your Metal Games

This session provides a comprehensive guide for game developers on optimizing Metal-based games for Apple platforms. The focus is on moving beyond quick performance checks to a sustained, data-driven workflow that covers the entire lifecycle of a game—from local development and playtesting to post-release monitoring in the field.

---

## Key Concepts, APIs, and Frameworks

*   **Metal Performance HUD:** An in-game overlay for real-time monitoring of metrics like FPS, frame interval, and memory usage. It supports custom configurations to show only relevant data.
*   **Instruments (Game Performance Overview):** A template for deep-dive analysis during desk-based development, capturing aggregated Metal metrics and CPU samples.
*   **MetalPerfChoice:** A new command-line tool (macOS 27) that allows developers to "look back" at system-recorded performance traces spanning hours or days, eliminating the need to have a debugger attached during the entire session.
*   **State Reporting API:** A new API that allows developers to define domains (e.g., levels, graphics settings, network status) to add semantic context to performance metrics.
*   **MetricKit:** A framework that provides in-process access to power and performance reports from end-user devices, including diagnostic data for crashes (e.g., memory exceptions).

---

## Techniques and Code Patterns

### 1. State Reporting
State reporting turns abstract data points into actionable insights by creating a finite state machine for your game's logic.

*   **Domains:** Represent functional areas of the game (e.g., `com.game.level`).
*   **Transitions:** Use `reportTransition` to mark changes between states.
    *   *Stable Metadata:* Immutable dictionaries used for descriptive info (e.g., Biome ID).
    *   *Volatile Metadata:* Updated via `reportVolatileMetadataUpdate` for values that change frequently within a state (e.g., player position).

**Basic Usage (Swift/Obj-C):**
```swift
// Defining and reporting a state
let reporter = StateReporter(domain: "com.game.level")
reporter.reportTransition(label: "Level2", stableMetadata: ["biome": "forest"])

// Updating frequently changing data
reporter.reportVolatileMetadataUpdate(["position": currentPos])
```

### 2. Post-Session Analysis
*   **Overview:** Run `MetalPerfTrace overview [tracefile]` to get a JSON or text-based statistical summary of the session.
*   **Aggregation:** Use `MetalPerfTrace` flags to group performance data by your defined states, allowing you to answer questions like, *"What is my average FPS specifically when graphics are set to High?"*
*   **Visualization:** Open traces in **Instruments** to see performance metrics overlaid with the `Points of Interest` instrument, which renders your State Reporting tracks visually.

---

## Practical Takeaways for Developers

1.  **Adopt State Reporting Early:** Design your state domains to be conceptually "orthogonal" (e.g., keep level logic separate from graphics settings). Avoid high-frequency transitions; limit updates to the cadence of user actions to prevent system throttling.
2.  **Leverage "Look Back" Collection:** You no longer need to be actively profiling to catch performance regressions. Since the system records metrics in the background, use `MetalPerfChoice` on macOS or "Performance Trace" in iOS Developer Settings to extract data after a playtest session ends.
3.  **Automate Regression Testing:** Utilize the `--JSON` output of `MetalPerfTrace` to feed performance data into automated scripts or AI agents to detect regressions in build-to-build performance.
4.  **Monitor in the Wild:** Integrate `MetricKit` to receive daily reports from your players. This is critical for identifying "silent" issues, such as memory exceptions or thermal throttling, that may not occur in your internal lab environment.
5.  **Contextualize Your Data:** Always pair performance drops with state information. A frame rate drop is just a number until you can link it to a specific in-game biome or an active graphics setting via the new State Reporting API.

<!-- resources -->

---

## Resources

- [Understanding the Metal Performance HUD metrics](https://developer.apple.com/documentation/Xcode/Understanding-metal-performance-hud-metrics)
- [Monitoring your Metal app’s graphics performance](https://developer.apple.com/documentation/Xcode/Monitoring-your-Metal-apps-graphics-performance)
- [Getting started with StateReporting](https://developer.apple.com/documentation/StateReporting/getting-started-with-statereporting)
- [Metal debugger](https://developer.apple.com/documentation/Xcode/Metal-debugger)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/388/4/682e727f-75f9-441f-81d9-2d6f38bde4b0/downloads/wwdc2026-388_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/388/4/682e727f-75f9-441f-81d9-2d6f38bde4b0/downloads/wwdc2026-388_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/388/
