# Explore enhancements to visionOS object tracking

# Session Summary: Explore Enhancements to visionOS Object Tracking

This WWDC 2026 session provides a comprehensive look at the evolution of spatial tracking in visionOS 27. It details how developers can leverage enhanced **Object Tracking** for precise spatial anchoring and how to create custom **Spatial Accessories** to enable low-latency, interactive physical-to-digital experiences.

---

### Key Concepts, APIs, and Frameworks

#### 1. Object Tracking Enhancements
*   **High Frame Rate Tracking:** Now supports tracking of fast-moving objects for better real-time understanding.
*   **Extended Training Mode (CreateML):** A new mode for training reference objects that improves accuracy and robustness, particularly for handheld items.
*   **Metric Space API:** New `ARKit` coordinate space corrections allow developers to query object poses in true metric space (unaffected by display corrections). This is critical for measuring tools (e.g., surgical probes).
*   **iOS Support:** Object tracking is now available on iOS, using the same reference object files as visionOS.

#### 2. Spatial Accessories
Spatial accessories are custom-built electronic devices containing a constellation of IR LEDs, an IMU (Inertial Measurement Unit), and a Bluetooth chip.
*   **Capabilities:** Provides sub-millisecond, low-latency tracking, robust performance under occlusion/low-light, and support for hardware inputs (buttons/touchpads) and haptics.
*   **Development Ecosystem:** Apple is introducing off-the-shelf development kits from partners (e.g., DF-robot, MicroE) to simplify prototyping.

---

### Techniques and Code Patterns

*   **Configuring High Frame Rate Tracking:**
    Use the new `ReferenceObjectConfiguration` API in `ARKit` to enable high frame rate tracking for a specific reference object before initiating the tracking session.
    ```swift
    // Example flow: Configure -> Create Session -> Run
    let config = ReferenceObjectConfiguration(highFrameRate: true)
    // Pass config when loading the reference object
    ```

*   **Handling Metric Poses:**
    When querying `ARAnchor` transforms, you can now toggle between visual alignment and physical accuracy.
    *   `.rendered`: Applies display corrections for visual alignment.
    *   `.none`: Returns the raw metric pose for precision measurement.

*   **Spatial Accessory Registration:**
    *   **Bundle Creation:** Requires a USDZ annotated with IMU/LED locations. Use the CLI tool to generate a `.referenceaccessory` file.
    *   **Info.plist Declaration:** Developers must declare their accessory as an exported (manufacturer) or imported (third-party) Uniform Type Identifier (UTI).
    *   **Discovery:** Use the `GC_SpatialAccessory` class to discover and connect to hardware, employing `updateAccessories` to switch devices seamlessly during runtime.

---

### Practical Takeaways for Developers

*   **Choosing the Right Approach:**
    *   **Use Object Tracking if:** You have a 3D model of an object (or can use a marker) and need accuracy for applications like measurement or spatial UI/UX.
    *   **Use Spatial Accessories if:** Your experience requires fast motion, low latency (e.g., racing/flight simulators), or requires physical tactile feedback (buttons/haptics).
*   **Optimization Tips:**
    *   Combine **High Frame Rate Tracking** with **Extended Training Mode** in CreateML for the most robust experience when tracking handheld objects.
    *   When designing custom hardware, spread LEDs in a unique, non-symmetrical pattern to ensure distinct orientation tracking.
*   **Validation:** Always use the **ARKit Accessory Tracking Debug View** (found in Developer Settings on the headset) to verify LED brightness, IMU latency, and sync performance before finalizing hardware builds.

<!-- resources -->

---

## Resources

- [Working with generic spatial accessories](https://developer.apple.com/documentation/visionOS/working-with-generic-spatial-accessories)
- [Preparing spatial accessories for tracking in your visionOS app](https://developer.apple.com/documentation/ARKit/preparing-spatial-accessories-for-tracking-in-your-visionos-app)
- [Spatial accessory design guidelines for Apple devices (section 20)](https://at.apple.com/vzqbpy)
- [Exploring object tracking with ARKit](https://developer.apple.com/documentation/visionOS/exploring_object_tracking_with_arkit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/283/4/22b92960-c65b-450f-b42c-6d6bff64a9b4/downloads/wwdc2026-283_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/283/4/22b92960-c65b-450f-b42c-6d6bff64a9b4/downloads/wwdc2026-283_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/283/
