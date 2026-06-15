# Dub Dub Daily: Day 5

# Session Summary: Dub Dub Daily (Day 5) – Swift 6.4 Highlights

The final day of WWDC 26 focused on the evolution of the Swift programming language. The session featured a discussion with Holly Borla, an engineering manager on the Swift team, who highlighted the primary goal for Swift this year: **reducing friction to help developers focus on writing clear, beautiful, and correct code.** The updates in Swift 6.4 center on refining the developer experience by removing legacy restrictions and improving the compiler's diagnostic capabilities.

---

### Key Concepts & Improvements
The core theme of Swift 6.4 is "addition by subtraction"—removing unnecessary syntax and compiler limitations to make the language more intuitive.

*   **Refined Language Syntax:** The compiler now handles "naturally written" code more gracefully. Specifically, combining `some` and `any` types with optionals no longer requires the extra parentheses that were previously mandatory.
*   **Concurrency Enhancements:** The compiler now allows the use of `async` methods within `defer` blocks, removing a previous limitation that hindered developers when handling asynchronous cleanup tasks.
*   **Diagnostic Overhaul:** A major focus was placed on improving compiler error messages. The goal is to ensure that when an error occurs, it is actionable and helps developers learn the language rather than causing frustration.
*   **Type-Checking Performance:** The team has made significant strides in reducing the occurrence of the infamous *"The compiler is unable to type-check this expression in reasonable time"* error. Efforts specifically targeted nested closures and SwiftUI view bodies.

### New Features & Attributes
*   **`@diagnose` Attribute:** This new feature gives developers granular control over compiler warnings and errors.
    *   **Suppression:** Allows for the suppression of specific deprecation warnings.
    *   **Granular Opt-in:** Enables developers to turn on strict concurrency or memory safety checks on a per-file or per-module basis, facilitating a smoother, incremental migration to Swift 6 language mode.

---

### Practical Takeaways for Developers
*   **Focus on Migration:** Use the new `@diagnose` attribute to migrate your codebase to Swift 6 language mode incrementally. By opting into strict concurrency diagnostics in isolated areas, you can address issues without needing to update your entire project at once.
*   **Community Collaboration:** The Swift team is transparent about their ongoing work regarding type-checker performance. Developers can monitor the [Swift open-source project](https://swift.org/) to track progress, view roadmaps, and contribute to the evolution of the language.
*   **Adopt "Natural" Coding Patterns:** Review code that previously required boilerplate syntax (such as complex type handling with optionals). You can now safely remove those workarounds, as the compiler has been updated to support standard, idiomatic usage.
*   **Leverage On-Demand Resources:** While the live sessions conclude, all group labs and technical sessions remain available on demand on the Apple Developer website.

### Conclusion
Swift 6.4 is designed to stay out of the developer's way. By prioritizing "invisible" improvements—such as better diagnostics and fewer syntax restrictions—the Swift team aims to increase overall productivity, allowing developers to spend less time fighting the compiler and more time creating great experiences.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/400/2/71024f51-e63d-4684-b97c-39b7b148238f/downloads/wwdc2026-400_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/400/2/71024f51-e63d-4684-b97c-39b7b148238f/downloads/wwdc2026-400_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/400/
