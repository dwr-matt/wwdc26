# Profile, fix, and verify: Improve app responsiveness with Instruments

# Summary: Profile, fix, and verify: Improve app responsiveness with Instruments

## Session Overview
This WWDC 2026 session provides a deep dive into using **Instruments 27** to diagnose and resolve common app responsiveness issues, such as dropped frames and application hangs. The speakers establish a structured "diagnostic flow" based on CPU utilization: determining whether the main thread is saturated (busy executing code), blocked (waiting on resources), or contending for shared actors. By using a note-taking application as a case study, the session demonstrates how to move from identifying a bottleneck to verifying a fix using advanced profiling features like Run Comparisons.

---

## Key Concepts, APIs, and Frameworks

### Core Instruments & Templates
*   **Time Profiler:** The starting point for high-level overview; tracks active CPU cycles and helps identify expensive code paths.
*   **System Trace:** Used when CPU utilization is low but the app is hanging; tracks OS-level events, thread states, and system calls (syscalls) to identify I/O or lock contention.
*   **Swift Executors Instrument (New):** Visualizes the Main Actor, global concurrent executors, and custom executors, making it easy to identify task congestion.
*   **Run Comparisons (New):** Allows developers to compare two different profiling traces side-by-side to see a performance delta (gains/regressions) for specific code paths.
*   **OS Signpost:** An API (`os_signpost`) used to mark custom intervals in your code, which Instruments then visualizes in the "Points of Interest" track.

### Analytical Modes
*   **Flame Graphs:** Maps call tree structures into spatial blocks, where the horizontal axis represents total CPU time, helping to spot expensive code paths visually.
*   **Top Functions:** A new mode that flattens the call hierarchy to show a list of functions sorted by their "self-weight," making it easier to identify hotspots that are called from many different locations.

---

## Demonstrated Code Patterns and Techniques

### 1. Eliminating Existentials for Performance
The session identifies that the Swift runtime function `swift_projectBoxOpaqueExistential` was a major hotspot. 
*   **The Issue:** Using `any Protocol` (existentials) causes runtime overhead due to type erasure.
*   **The Fix:** Refactor code to use **concrete types** or **generics**. This allows the compiler to optimize the code more effectively, eliminating the need for runtime unwrapping.

### 2. Moving Work off the Main Actor
*   **The Issue:** Heavy tasks (like thumbnail rendering) inherited the `MainActor` context because they were triggered by SwiftUI, causing UI freezes.
*   **The Fix:** Explicitly move work to the background using `Task { @MainActor in ... }` or, better yet, by using the `@Concurrent` attribute (or custom task initializers) to route work to the **global concurrent executor**.

### 3. Asynchronous I/O
*   **The Issue:** Performing synchronous file writes (e.g., `data.write(to: ...)`) on the main thread blocks execution until the disk responds.
*   **The Fix:** Wrap intensive I/O operations in an asynchronous `Task` to keep the main thread free for UI events.

---

## Practical Takeaways for Developers

*   **Always Profile Release Builds:** Debug builds contain extra overhead for "debuggability" that masks true performance characteristics. Always use `Product > Profile` to capture actionable data.
*   **Follow the CPU Diagnostic Flow:**
    *   **High CPU Usage:** Your code is too slow. Use *Time Profiler* and *Top Functions* to find the bottleneck.
    *   **Low CPU Usage:** Your thread is likely blocked. Use *System Trace* to see if the thread is waiting for disk I/O, IPC, or locks.
    *   **Main Actor Contention:** Use the *Swift Executors* instrument to see if too many tasks are queuing up on the main thread.
*   **Use Comparisons:** When you optimize, don't guess. Use the new **Run Comparison** tool to cross-reference your optimized trace with a baseline trace to ensure your changes actually improved performance without introducing regressions.
*   **Instrumentation is Documentation:** Add `os_signpost` calls early in development to label critical workflows (e.g., "Saving," "Lasso Selection"). This makes it significantly easier to filter and analyze specific user actions in Instruments later.

<!-- resources -->

---

## Resources

- [Analyzing CPU profiles with call tree views](https://developer.apple.com/documentation/Xcode/analyzing-cpu-profiles-with-call-tree-views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/268/4/7d94575d-e65b-4033-811f-199586ac587a/downloads/wwdc2026-268_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/268/4/7d94575d-e65b-4033-811f-199586ac587a/downloads/wwdc2026-268_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/268/
