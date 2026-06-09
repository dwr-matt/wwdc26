# What’s new in Wallet

# WWDC 2026: What’s New in Wallet

This session introduces significant enhancements to the Apple Wallet ecosystem, focused on improving the visual appeal of passes, expanding barcode support, and providing powerful new developer tooling for creation and management. With the release of iOS 27, developers have new ways to create rich, branded experiences while maintaining backward compatibility for legacy devices.

---

### Key Concepts, APIs, and Frameworks

*   **Poster-Generic Style:** A new top-level pass style designed for membership and loyalty cards that allows for bold, edge-to-edge artwork.
*   **Expanded Barcode Support:** iOS 27 adds native support for four additional barcode types: **EAN 13, Code 39, Codabar, and ITF**. 
*   **Featured Actions:** A new, flexible API that allows developers to surface up to two custom call-to-action buttons (e.g., "View Benefits") directly below the pass face. 
*   **Pass Designer:** A new macOS application that provides a "What You See Is What You Get" (WYSIWYG) interface for designing and prototyping passes visually.
*   **Pass Builder:** A new Swift-on-server package and command-line interface (CLI) tool that automates the programmatic generation, personalization, and signing of passes for distribution at scale.

---

### Code Patterns and Techniques

#### 1. Managing Backward Compatibility
When adopting the `poster-generic` style or new barcode formats, Apple recommends maintaining compatibility for users on iOS 26 and earlier:
*   **Style Fallback:** Include both the `poster-generic` and the existing `generic` top-level keys in your pass JSON. 
*   **Barcode Priority:** Use the `barcodes` array to provide multiple formats in order of preference. The system will select the first format that it can render based on the OS version, preventing broken scanning experiences.

#### 2. Implementing Featured Actions
Actions are defined in the top-level of the pass JSON using the `featuredActions` key:
```json
"featuredActions": [
  {
    "identifier": "unique-id",
    "type": "membershipBenefits",
    "value": "https://example.com/benefits"
  }
]
```

#### 3. Scaling with Pass Builder
The `Pass Builder` framework simplifies the complex manual process of signing passes (creating a manifest, generating a detached signature, and compressing the bundle). 
*   **Type Safety:** It uses the `PassPackage` type to interact with the pass bundle programmatically in Swift.
*   **Server-Side Signing:** Developers load certificates using the `PassCertificate` type, instantiate a `PassSigner`, and call `signPass` to generate production-ready `.pkpass` files.
*   **Cross-Platform Interop:** Using Swift-Java interoperability or protobuf definitions, developers can generate type-safe models for non-Swift environments and invoke the `Build Pass` CLI to finalize and sign passes.

---

### Practical Takeaways for Developers

*   **Prioritize Manual Entry:** If your use case involves a single barcode type that is not universally supported, ensure the credential ID is surfaced in a header or primary field. Ensure staff are trained for manual entry to prevent service disruptions.
*   **Curate Actions:** Only include the most relevant user actions in `featuredActions`. Since you are limited to two, choose ones that drive the highest engagement.
*   **Workflow Integration:** Use **Pass Designer** for initial prototyping and branding. Once the design is locked, use **Pass Builder** within your backend services to fetch data from your database (e.g., customer photos or loyalty points) and generate personalized passes on-the-fly.
*   **Testing:** Always test your passes across different OS versions to ensure that fallbacks (both for styles and barcodes) function as expected.