# Migrate to Swift Testing

# Session Summary: Migrate to Swift Testing

## Overview
This WWDC 2026 session provides a roadmap for developers to transition their existing test suites from `XCTest` to the modern, expressive **Swift Testing** framework. The session emphasizes a low-risk migration strategy, leveraging **Test Framework Interoperability** to allow both testing frameworks to coexist within the same project. It also introduces advanced testing features—such as parameterized tests and exit tests—that enable developers to write more powerful and efficient tests as they modernize their codebases.

---

## Key Concepts, APIs, and Frameworks

### Core Building Blocks
*   **`@Test` Macro:** The fundamental annotation used to define a test function. Swift Testing supports backticks (raw identifiers) for readable test names containing spaces or punctuation.
*   **`#expect(...)` Macro:** The primary assertion tool, designed to be flexible and replace most traditional `XCTAssert` calls.
*   **`Issue.record()`:** The Swift Testing equivalent of `XCTFail`, used for unconditional failure reporting.
*   **Test Framework Interoperability:** A feature allowing developers to use Swift Testing APIs within `XCTest` classes and vice versa, enabling incremental migration.

### Interoperability Modes
To manage cross-framework issues (where an API from one framework is used in a test from the other), Xcode provides configurable modes in the Test Plan:
*   **Limited (Default for legacy):** Cross-framework issues appear as warnings.
*   **Complete:** Elevates cross-framework issues to errors.
*   **Strict:** Stops test execution immediately upon encountering a cross-framework issue (replaces warnings/errors with a fatal error).
*   **None:** Opt-out mode (not recommended, as it hides potential bugs).

---

## Demonstrated Patterns & Techniques

### Migration Strategy
*   **Incremental Updates:** Instead of a full rewrite, leave existing `XCTest` suites in place and write new features using Swift Testing.
*   **Helper Functions:** If you have shared test helpers that call `XCTFail`, you can continue to use them while migrating, provided you configure the Interoperability mode to handle the resulting cross-framework calls.
*   **Code Coverage:** The session demonstrates how to use **Exit Tests** to cover edge cases like `precondition` failures.

### Advanced Testing Tools
*   **Parameterized Tests:** By passing arguments to the `@Test` macro, Swift Testing automatically generates multiple test cases (one for each argument combination). These run in parallel by default, significantly improving execution speed.
*   **Exit Tests:** Using `#expect(exitsWith: ...)`, developers can test code that causes a process crash or exit (e.g., `fatalError` or `precondition` failure). The test runs the suspect code in an isolated child process, ensuring the crash does not impact the main test suite.

---

## Practical Takeaways for Developers

1.  **Don't Rush:** You do not need to delete your `XCTest` suites. Focus on writing new tests with Swift Testing and migrating existing ones only as you need to modify them.
2.  **Use Interoperability:** Enable `Complete` or `Strict` mode in your Test Plan settings to ensure you are alerted to outdated `XCTest` patterns that should be replaced with Swift Testing equivalents.
3.  **Modernize Assertions:** Replace standard `XCTAssert` logic with `#expect` and leverage the `require` macro when you need to halt a test immediately upon failure (replacing `continueAfterFailure = false`).
4.  **Leverage Xcode:** The Xcode coding assistant can help formulate a migration strategy and even automate parts of the conversion process.
5.  **Community & Evolution:** Swift Testing is open source (SwiftLang organization on GitHub). It is governed by the Swift Testing workgroup and follows the Swift Evolution process for new features.
6.  **Maintain Compatibility:** Note that UI automation, performance testing, and testing code that throws Objective-C exceptions still require `XCTest`.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/267/4/d54e4861-10d9-4d4d-9952-3fe311cd2dc4/downloads/wwdc2026-267_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/267/4/d54e4861-10d9-4d4d-9952-3fe311cd2dc4/downloads/wwdc2026-267_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/267/
