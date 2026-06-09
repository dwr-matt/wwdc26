# Design immersive environments for visionOS apps and the spatial web

# Session Summary: Design Immersive Environments for visionOS

## Overview
This WWDC 2026 session, presented by Apple human interface designer Michael Breiman, provides a comprehensive guide to building photorealistic, immersive 3D environments for visionOS. Rather than treating environments as flat backdrops, the session emphasizes creating spaces with true depth, parallax, and life-like movement. The workflow is divided into three distinct phases—**Pre-production, Production, and Post-production**—to help developers build environments that are not only visually stunning but also optimized for spatial computing.

---

## Key Concepts
*   **Intent-Driven Design:** Before building, define the purpose. Are you creating a cinematic space for media, a distraction-free zone for focus, or a realistic backdrop for a web-based experience?
*   **Spatial Fidelity:** Environments require a sense of scale and depth. This is achieved through 360-degree photography, accurate geometry, and 81-degree field-of-view considerations.
*   **The Power of Layers:** Think of environments in layers (background, mid-ground, foreground) to control composition and focus.
*   **Performance vs. Realism:** To maintain high frame rates in visionOS, developers must balance high-resolution assets with efficient, pre-computed rendering techniques.

---

## Technical Techniques and Workflows

### 1. Pre-Production & Scouting
*   **Location Scouting:** When using real-world locations, identify the "primary viewpoint" and consider what the user sees when turning around (the full 360-degree field).
*   **Reference Gathering:** Use photography, videography, and digital elevation models (DEMs) to ensure accuracy. When real locations are inaccessible (e.g., the Moon), rely on historical mission photography.
*   **Physical Modeling:** Build scale models of your scene to test lighting scenarios (e.g., how the Sun hits planetary moons) to uncover necessary design requirements early.

### 2. Production (Asset Capture)
*   **Capture Standards:** To achieve the required 40 pixels per degree of visual sharpness, target a resolution of **14,400 x 7,200 pixels** for a full 360-degree panorama.
*   **Hardware Setup:** Use a leveled tripod (1m high) and a secondary camera (2m high) to capture extra vertical data for filling in occluded areas during post-production.
*   **Data Collection:** Use bracketed exposures for high dynamic range (HDR), and capture Macbeth charts, chrome/gray spheres, and video of moving elements for reference.

### 3. Post-Production (Optimization & Shaders)
*   **Cleanup:** Utilize digital matte painting and CG replacement to remove unwanted elements (camera rigs, people, or cluttered vegetation) that break immersion.
*   **Shader Optimization:**
    *   **Flow Maps:** Use UV flow maps to simulate cloud movement and swaying foliage without expensive real-time vertex calculations.
    *   **Shadow Masking:** Instead of rendering real-time light shadows, use scrolling masks to darken terrain textures.
    *   **Flipbook Textures:** Use pre-rendered textures for complex shadow patterns.
    *   **Hierarchical Animation:** Stack low-frequency and high-frequency sine waves to create natural, non-repetitive motion in plant life.
*   **Visual Integrity:** Perform "A-B comparisons" between raw photography and 3D assets to ensure texture quality is maintained. Use extreme gamma/gain adjustments to test for value inconsistencies.

---

## Practical Takeaways for Developers

*   **Plan for Removal:** It is much easier to add CG elements to a clean scene than to remove real-world obstructions. Choose viewpoints that frame the horizon cleanly.
*   **Audio is Half the Experience:** Spatial audio is critical for immersion. Attach spatial emitters to moving parts of the environment (e.g., flowing water or rustling leaves) to "ground" the visuals.
*   **Iterate and Break:** Do not get attached to a single approach. If the environment feels flat, try "flopping" the scene or testing it under extreme lighting values to identify flaws.
*   **Start Small:** Even with limited resources, prioritize high-quality source photography and thoughtful composition over complex, unoptimized real-time shaders.
*   **Further Learning:** The session suggests looking into "Optimize Your Custom Environments for visionOS" for technical details on real-time rendering budgets and performance monitoring.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/234/4/88f2dbdd-e1b1-4b50-9fa0-69a32ac768b2/downloads/wwdc2026-234_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/234/4/88f2dbdd-e1b1-4b50-9fa0-69a32ac768b2/downloads/wwdc2026-234_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/234/
