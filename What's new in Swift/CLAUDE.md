# What's New in Swift (Swift 6.3 / 6.4) — Session Notes

Sources:
- Transcript: full session (Becca + Evan from Swift team)
- Documentation: https://developer.apple.com/tutorials/data/documentation/swift.json (index only — no per-feature code pages available)
- Code patterns below are from transcript descriptions. Structural patterns for well-known attributes (@inline, @specialized) are standard Swift — treat as verified. Newer APIs (Ref, MutableRef, borrow/mutate accessors) are transcript-described; verify against release notes before use.

---

## Four Areas

1. **Language improvements** — syntax cleanup, availability, `@diagnose`, module selectors
2. **Library updates** — standard library, Swift Testing, Subprocess 1.0, Foundation
3. **Cross-platform** — Swift→C interop, Android, WASM, Embedded Swift
4. **Performance** — optimizer control, ownership system (borrow/mutate, Ref/MutableRef, Iterable)

---

## 1. Language Improvements

### Minor syntax cleanup (Swift 6.4)
- `some T?` — no more parentheses around optional in `some`/`any` position
- `defer` blocks can now contain `async` calls
- Warning when a Swift concurrency task's thrown error is silently ignored
- `weak let` — immutable weak reference, participates in `Sendable` checking
- `~Sendable` — explicitly opt a type OUT of Sendable; subclasses can still be Sendable
- Struct with mixed `internal` + `private` properties → second memberwise init generated for external files

### `any Apple OS` availability

```swift
// Before — repeat for every platform
@available(iOS 26.0, macOS 26.0, tvOS 26.0, watchOS 26.0, visionOS 26.0, *)

// After
@available(anyAppleOS 26.0, *)

// With carve-outs
@available(anyAppleOS 26.0, *)
@available(watchOS, unavailable)
```

Also works for `#if os(...)` compile conditions.

### `@diagnose` — per-declaration diagnostic control

```swift
// Suppress deprecated warning in one place while migrating
@diagnose(ignore: deprecatedDeclaration)
func migratingSlowly() { ... }

// Enable strict memory safety for security-critical functions only
@diagnose(enable: strictMemorySafety)
func securityCritical() { ... }

// Treat future-error warnings as errors right now
@diagnose(asError: concurrencyWarning)
func lateMigration() { ... }
```

Also used for Embedded Swift: suppress `embeddedRestrictions` in functions that expose features unavailable in embedded contexts.

### Module Selector `::` (Swift 6.3)

Problem: `Rocket.SaturnV` is ambiguous when module `Rocket` also has a *type* named `Rocket` — Swift prefers type names over module names, so it looks for `Rocket.SaturnV` as a *member*, not a module path.

```swift
// :: always treats left side as a module name
Rocket::SaturnV()       // module Rocket → type SaturnV
GiftShopToys::SaturnV() // module GiftShopToys → type SaturnV

// Also works on methods (resolves identically-named extension methods from two modules)
value.ModuleA::transform()
```

Use defensively in macro expansions and generated code. Don't intentionally design conflicting APIs.

---

## 2. Library Updates

### Task Cancellation Shield (standard library, beta)

```swift
// Continue critical work even after Task cancellation
await withTaskCancellationShield {
    try await finalizeWriteToDisk()  // Task.isCancelled always false inside here
}
// Keep the shield scope short: finish or rollback only
```

### `mapKeyedValues` — dictionary transformation with key access

```swift
// mapValues only gives the value
let mapped = dict.mapValues { transform($0) }

// mapKeyedValues gives key and value
let mapped = dict.mapKeyedValues { key, value in
    transform(key: key, value: value)
}
```

### `FilePath` — standard library cross-platform path type

Based on the type from Swift System. Handles platform-specific path representations safely.

### Swift Testing (Swift 6.4)

- `issue.record(severity: .warning)` — surface non-fatal issues without blocking CI
- `test.cancel()` — dynamically cancel individual parameterized test arguments
- `swift test --repeat-until-pass <n>` — retry flaky tests; only failing tests rerun

**XCTest ↔ Swift Testing bidirectional interop:**
- `XCTAssert*` failures called from Swift Testing → reported as test issues
- `#expect` called from an `XCTestCase` → works with consistent behavior
- Cross-calling currently reported as *warnings* by default; opt into failures in Xcode build settings

### Subprocess 1.0

```swift
// stdout/stderr as async buffer sequences — stream line by line
let execution = try Subprocess.run(...)
for await line in execution.standardOutput.strings {
    print(line)  // respects grapheme cluster boundaries — no split multibyte chars
}
```

### Foundation

- `Data`: faster span access, equality, iteration, mutation; faster `NSData` bridging on Apple platforms
- `NSURL` + `CFURL` unified to a single Swift implementation
- `ProgressManager` — new type designed for async/await; separates progress *composition* from progress *reporting*; type-safe metadata attachment

---

## 3. Cross-Platform

### Swift → C interop (`@C`, `@implementation`) — Swift 6.4

```swift
// Implement an existing C function (declared in .h) — no new C declaration emitted
@C @implementation
func launchWindowLength(_ window: LaunchWindow) -> Double {
    window.end - window.start
}

// New Swift function exposed to C — compiler emits declaration into generated header
@C
func averageLaunchWindowLength(_ windows: Span<LaunchWindow>) -> Double {
    windows.reduce(0) { $0 + launchWindowLength($1) } / Double(windows.count)
}
```

- `@C` mirrors `@objc` — exports Swift function to C
- `@implementation` — tells compiler "this implements an existing C declaration; don't add a new one"
- `Span` auto-bridges to C pointer+count — type-safe

**C++20 `std::span` ↔ Swift `Span` bridging** also supported in Swift C++ interop.

### Android (Swift 6.3)

- Official Swift SDK for Android at swift.org
- `swift-java` package: call async and throwing Swift functions from Java/Kotlin
- Constrained extensions, Java classes conforming to Swift protocols

### WebAssembly

- JavaScriptKit safe bridging: 35–40× faster than dynamic bridging (GoodNotes production benchmark)
- Same Swift codebase → native iOS app + web (WASM) + back-end server

### Embedded Swift

- Now supports existential types (`any Protocol`, heterogeneous arrays)
- Untyped `throws` (via same existential machinery)
- DWARF debug metadata embedded in debug info — keeps binary size down, dramatically improves core dump debugging

---

## 4. Performance Tuning

### Optimizer control attributes

```swift
// Force inlining even when optimizer says no
// Pair with `final` for class methods (virtual dispatch blocks inlining)
@inline(always)
final func hotPath() { ... }

// Prevent inlining always
@inline(never)
func rareErrorPath() { ... }

// Pre-generate specialized versions for known types (Swift 6.3)
// Useful for library functions the compiler can't see usages of
@specialized(where T == Int)
@specialized(where T == Double)
func process<T>(_ values: [T]) -> T { ... }
```

### `borrow` / `mutate` accessors — replace `get` / `set` (Swift 6.4)

`get`/`set` copy data in and out. For a 2 KB struct, mutating one `Int` copies the whole thing twice.

```swift
struct UniqueBox<T> {
    private var _ptr: UnsafeMutablePointer<T>

    var value: T {
        borrow { yield _ptr.pointee }   // read-only, no copy
        mutate { yield &_ptr.pointee }  // exclusive in-place write
    }
}
```

- `borrow` — both sides read-only during the borrow; no copy
- `mutate` — caller has exclusive access; other side fully blocked
- Also enables non-copyable types as property types

### `Ref` / `MutableRef` — storable borrow/mutation (Swift 6.4)

Like `Span` but for a single value. Lets you hold a dictionary lookup "open" across a loop:

```swift
// Before: dictionary lookup on every iteration
for event in events {
    counts[event.type, default: 0] += 1
}

// After: one lookup, held open via MutableRef
var countRef: MutableRef = &counts[event.type, default: 0]
for event in events {
    countRef += 1
}
// access ends when countRef goes out of scope
```

- `Ref` — stores a borrow (read-only)
- `MutableRef` — stores a mutation (exclusive write)
- Both are **non-escapable** — Swift enforces the access ends at scope exit
- Can be passed/returned from functions and used in generic types

### `Iterable` protocol — borrow-based `for` loops (Swift 6.4)

`Sequence` copies elements out. `Iterable` lends them as batches via `Span`:

- Supports non-copyable elements
- No reference counting when iterating over objects or COW types
- Can throw during iteration (like `AsyncSequence`)
- `for` loop prefers `Sequence` if available; falls back to `Iterable`
- Prohibits mutating the iterable while looping (enforced by exclusivity)

### New standard library types

| Type | Description |
|---|---|
| `UniqueBox<T>` | Manages a pointer to a large value; use with `borrow`/`mutate` |
| `UniqueArray<T>` | Non-copyable array; stores non-copyable elements; no refcount overhead |
| `withTemporaryAllocation` | Uses `OutputSpan` instead of `UnsafeMutableBufferPointer` |
| `Continuation` | Compile-time verified single-resume (stricter than `CheckedContinuation`) |

---

## Summary Table

| Feature | Version | Category |
|---|---|---|
| `any Apple OS` availability | 6.4 | Language |
| `@diagnose` | 6.4 | Language |
| Module selector `::` | 6.3 | Language |
| `weak let` + `~Sendable` | 6.4 | Language |
| `withTaskCancellationShield` | 6.4 | Stdlib |
| `mapKeyedValues` | 6.4 | Stdlib |
| `FilePath` | 6.4 | Stdlib |
| Swift Testing XCTest interop | 6.4 | Testing |
| Subprocess 1.0 | 6.4 | Tooling |
| `@C` / `@implementation` | 6.4 | Interop |
| Android SDK | 6.3 | Platforms |
| `@inline(always)` | 6.4 | Performance |
| `@specialized` | 6.3 | Performance |
| `borrow`/`mutate` accessors | 6.4 | Performance |
| `Ref` / `MutableRef` | 6.4 | Performance |
| `Iterable` protocol | 6.4 | Performance |
| `UniqueBox`, `UniqueArray` | 6.4 | Performance |

---

## Resources
- [Swift Blog](https://www.swift.org/blog/)
- [Swift Documentation](https://www.swift.org/documentation/)
- [Swift Forums](https://forums.swift.org)
- Session page: https://developer.apple.com/videos/play/wwdc2026/262/
