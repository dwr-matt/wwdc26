# What's new in Swift

# WWDC 2026: What’s New in Swift

This session covers the latest advancements in Swift 6.3 and 6.4, focusing on language refinements, library updates, enhanced interoperability, and sophisticated performance-tuning tools. The speakers detail how the Swift team is streamlining day-to-day development, improving cross-platform support (including WebAssembly, Android, and embedded systems), and expanding the ownership system to provide high-performance, memory-safe alternatives to unsafe pointers.

---

### Key Concepts, APIs, and Frameworks

#### 1. Language Improvements
*   **Syntax Streamlining:** Optional types in `some` or `any` no longer require parentheses.
*   **Error Handling:** You will now receive a warning if you silently ignore errors from Swift concurrency tasks.
*   **Concurrency:** Async functions are now allowed in `defer` blocks.
*   **Sendable Enhancements:**
    *   `weak let` properties now participate in `Sendable` checking.
    *   New `~Sendable` syntax allows explicit opt-out of `Sendable` requirements.
*   **Memberwise Initializers:** Structs with mixed `internal` and `private` properties now generate a second memberwise initializer accessible from other files.
*   **Availability:** A new `any Apple OS` attribute condenses platform-specific availability checks.
*   **Module Selectors:** The `::` syntax (e.g., `Module::Type`) resolves naming conflicts between modules, overriding standard type-name preferences.

#### 2. Library & Tooling Updates
*   **Standard Library:**
    *   **Task Cancellation Shield:** A block that prevents cancellation checks, allowing critical cleanup work to finish.
    *   **`mapKeyedValues`:** New dictionary transformation that provides both the key and the value to the closure.
    *   **`FilePath`:** Unified type for safe file path manipulation.
    *   **`UniqueBox`:** A new standard library type that manages storage for large values, preventing unnecessary copies.
*   **Swift Testing:**
    *   Supports severity levels for `issue.record` (warnings vs. failures).
    *   Dynamic test cancellation via `test.cancel`.
    *   `XCTest` interoperability (running `XCTest` assertions within `Swift Testing` and vice versa).
*   **Subprocess 1.0:** Refined API for launching processes, including async buffer sequences for streaming `stdout`/`stderr`.
*   **Foundation:** Re-written in modern Swift, providing faster `Data` iteration, mutation, and unified `NSURL`/`CFURL` implementations.

#### 3. Interoperability & Cross-Platform Support
*   **Swift-to-C:** The new `@C` attribute allows exporting Swift functions to C. Use `@implementation` to provide the body of a C function declared in a header file.
*   **Android:** Official Swift SDK for Android is available, with improved Java/Kotlin interoperability (calling async/throwing functions).
*   **WebAssembly (WASM):** Improved `JavaScriptKit` bridging (35-40x performance gains) makes running Swift on the web via WASM highly efficient.
*   **Embedded Swift:** Now supports existential types, untyped `throws`, and improved debugging via DWARF metadata.

---

### Code Patterns and Techniques

*   **Compiler Control:**
    *   `@inline(always)` and `@inline(never)`: For forcing or preventing function inlining.
    *   `@specialized(where ...)`: Explicitly generates specialized generic code for specific types to avoid generic overhead.
*   **Ownership & Accessors:**
    *   `borrow` and `mutate` accessors replace `get` and `set` to provide direct access to storage without costly copying.
    *   `Ref` and `MutableRef`: New types that act as "pointers" to single values (similar to `Span`), allowing for high-performance, scope-safe memory access.
*   **Diagnostic Control:**
    *   `@diagnose`: Allows granular control over warnings/errors within specific declarations (e.g., suppressing deprecation warnings in a single file or enabling strict memory checks for security-critical functions).

---

### Practical Takeaways for Developers

1.  **Prioritize Safety over `Unsafe`:** Stop using unsafe pointers for performance. Utilize `borrow`, `mutate`, and the new `Ref` types to achieve the same performance gains while maintaining full compile-time memory safety.
2.  **Modernize Builds:** Switch to the `Swift Build` system (now the default for Swift Package Manager) for better consistency between Xcode and command-line builds.
3.  **Optimize Binaries:** For performance-sensitive code, use `@specialized` to guide the compiler’s optimization for frequently used types and `@inline(always)` for critical paths.
4.  **Adopt Swift Testing:** Use the new interop features to start migrating your `XCTest` suites to `Swift Testing` incrementally, as they can coexist seamlessly during the transition.
5.  **Utilize Module Selectors:** Use the `Module::Type` syntax if you encounter module naming collisions (e.g., using third-party packages with overlapping class names).
6.  **Embedded/Web Readiness:** If you have high-performance Swift code, consider compiling it to WASM for web usage or using the `Embedded Swift` subset for resource-constrained hardware.

<!-- resources -->

---

## Resources

- [Swift Blog](https://www.swift.org/blog/)
- [Explore documentation on swift.org](https://www.swift.org/documentation/)
- [Swift Forums](https://forums.swift.org)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/262/5/d430e425-34fc-4ed5-b590-507ac593453a/downloads/wwdc2026-262_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/262/5/d430e425-34fc-4ed5-b590-507ac593453a/downloads/wwdc2026-262_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/262/
