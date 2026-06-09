# Meet the new MetricKit

# WWDC 2026: Meet the new MetricKit

This session introduces a reimagined MetricKit framework for iOS 27, designed to provide developers with a modern, Swift-first approach to monitoring app health and performance in the real world. By moving beyond aggregate, top-level data, the new MetricKit allows developers to correlate performance bottlenecks—such as hangs, memory exceptions, and scroll hitches—with specific user flows and app states.

---

### Key Concepts, APIs, and Frameworks

*   **MetricKit (iOS 27 Rebuild):** The framework has been entirely rebuilt with a modern, expressive Swift-first API. Developers currently using the older `MXMetricManager` are encouraged to migrate to these new APIs to access exclusive features.
*   **Metrics vs. Diagnostics:**
    *   **Metrics:** Ongoing health signals (e.g., launch times, CPU/GPU usage, Metal frame rates) used to track performance trends over time.
    *   **Diagnostics:** Detailed, actionable reports (e.g., crash backtraces, memory termination reports) that identify specific code paths causing failures.
*   **State Reporting Framework:** A new system that allows developers to tag app usage with meaningful contexts (e.g., current tab, active user flow, or experimental feature flags). This enables granular analysis of metrics per-state rather than just global averages.
*   **Domains:** A grouping mechanism for states. Each domain represents a specific area of the app (e.g., "Reports" tab vs. "Spending" tab), and each domain can only have one active state at a time.

---

### Code Patterns and Techniques

*   **MetricManager Initialization:** The entry point for receiving data. It must be initialized at `AppStartup` to avoid data loss and should be kept in memory to ensure report streams remain active.
    ```swift
    // Pseudocode pattern
    let manager = MetricManager()
    // Subscribe to streams
    await manager.metricReports
    await manager.diagnosticReports
    ```
*   **JSON Encoding for Backend Ingestion:** Because `MetricReport` and `DiagnosticReport` are `Codable`, developers can easily encode them into JSON to send to their own analytics servers.
*   **Advanced Filtering:** Developers can iterate through `intervalEntries` (daily or windowed breakdowns) and switch over metric cases (e.g., `.memory`, `.cpu`) to access granular data points like peak memory usage.
*   **Contextual Reporting:** When reporting states, developers can use the `@ReportableMetadata` macro to attach custom structured data (e.g., list size, sort order) to states, providing rich context for performance regressions.
*   **Encoding by Domain:** When sending data to a server, the `JSONEncoder` can be configured using `userinfo` to group entries by `stateReportingDomain`, making it easier to analyze performance for specific user scenarios.

---

### Practical Takeaways for Developers

1.  **Shift to Proactive Monitoring:** Use the daily metric reports to establish a baseline for your app. If "Time-to-first-draw" or "Hang rate" trends negatively, use the granular state data to pinpoint if the regression is isolated to a specific UI feature.
2.  **Utilize Memory Diagnostics:** iOS 27 now provides deeper insights into memory termination. If your app is being killed by the system, look for the new memory exception diagnostics to see exactly which code path triggered the OOM (Out of Memory) event.
3.  **Optimize for Metal:** Game developers should leverage the new **Metal Frame Rate** metric to correlate rendering performance with user-defined app states.
4.  **Strategic State Management:** 
    *   **Keep it meaningful:** Do not track every transient UI interaction. Track stable, high-level phases (e.g., "Scanning Receipt" vs "Viewing Dashboard").
    *   **Avoid over-granularity:** Too many states can lead to "data noise," making it harder to spot statistically significant trends.
5.  **Validation:** Always use the **Points of Interest** instrument during testing to verify that your app’s state reporting logic correctly maps to the expected user experience before shipping to the App Store.

<!-- resources -->

---

## Resources

- [Getting started with StateReporting](https://developer.apple.com/documentation/StateReporting/getting-started-with-statereporting)
- [Analyzing app performance with MetricKit](https://developer.apple.com/documentation/MetricKit/analyzing-app-performance-with-metrickit)
- [Monitoring app performance with MetricKit](https://developer.apple.com/documentation/MetricKit/monitoring-app-performance-with-metrickit)
- [Track performance by app state using MetricKit](https://developer.apple.com/documentation/MetricKit/track-performance-by-app-state-using-metrickit)
- [MetricKit](https://developer.apple.com/documentation/MetricKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/222/4/86b76599-f095-4bd8-8004-f1dbd1bacb84/downloads/wwdc2026-222_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/222/4/86b76599-f095-4bd8-8004-f1dbd1bacb84/downloads/wwdc2026-222_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/222/
