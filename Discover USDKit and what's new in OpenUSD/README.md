# Discover USDKit and what’s new in OpenUSD

# Summary: Discover USDKit and what’s new in OpenUSD

## Overview
This session introduces **USDKit**, a powerful new Apple framework designed to make working with Universal Scene Description (USD) in Swift a first-class experience. The presentation outlines Apple’s deepening commitment to OpenUSD as the foundation for spatial computing across the Mac, iPhone, iPad, and Vision Pro. Beyond the new framework, the session covers significant updates to system-level 3D tools (Preview, Quick Look), advancements in 3D representation (Gaussian splats), and new capabilities for web-based 3D content via Safari.

---

## Key Concepts, APIs, and Frameworks

*   **USDKit:** A new system framework that provides native Swift bindings for USD. It is designed to be accessible for newcomers while offering familiar concepts for experienced USD developers.
*   **OpenUSD Foundations:** Apple continues to contribute to the open-source ecosystem, including integrating **MaterialX** (for materials) and **OpenVDB** (for volumetric data).
*   **Spatial Preview Framework:** A new framework on macOS that enables live, collaborative synchronization between Mac applications and visionOS devices via Quick Look and SharePlay.
*   **Gaussian Splats (Particle Fields):** A new USD primitive type capable of rendering high-fidelity, real-world captured scenes as fuzzy, light-responsive particles.
*   **OpenPBR:** A significant upgrade over the legacy "USD Preview Surface," providing physically accurate material rendering across Apple’s platforms.
*   **Web Integration:** The new `<model>` tag in Safari allows for native embedding of 3D content in web pages, with seamless transition to spatial viewing on visionOS.

---

## Code Patterns and Techniques

The session demonstrated a standard workflow for programmatic USD manipulation using USDKit:

1.  **Stage Management:** Developers can load an existing file using `USDStage.open(url:)`.
2.  **Composition via Referencing:** Instead of baking data into a file, developers use `references` to link assets. This keeps files lightweight and ensures that any updates made to the source asset are automatically reflected in the parent scene.
3.  **Hierarchy Traversal & Transformation:** USDKit provides high-level methods like `addTransformOperation`, which automates the complexities of setting transform attributes and maintaining the correct operation order.
4.  **Accessibility Metadata:** Developers can now apply an accessibility schema to 3D prims. By adding specific labels and descriptions, developers can make spatial content discoverable for assistive technologies.
5.  **Compression:** To manage large files, USDKit includes an `exportPackage` method. By enabling mesh (new codec) and texture (AVIF) compression, developers can reduce asset sizes by up to 90% without significant quality loss.

---

## Practical Takeaways for Developers

*   **Choose the Right Tool:** 
    *   **USDKit:** Use for building native Swift/RealityKit applications.
    *   **Swift USD:** Use for open-source cross-platform Swift projects.
    *   **OpenUSD (C++):** Use for advanced, cross-platform engine development.
*   **Preview as a Pro Tool:** The Mac Preview app has been overhauled to function as a lightweight 3D editor. It supports scene manipulation, lighting adjustments, and a new high-fidelity ray tracer, effectively reducing the need to jump into heavy 3D software for simple tasks.
*   **Prioritize Accessibility:** Apple has established industry standards for 3D accessibility metadata. Implementing this in your apps ensures your 3D content is inclusive and future-proof.
*   **Embrace the Pipeline:** If you are an app developer, focus on **USDKit** for your integration needs. It is the core technology powering all of Apple's recent 3D advancements, ensuring that your app benefits from future system updates and performance improvements automatically.