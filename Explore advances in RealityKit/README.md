# Explore advances in RealityKit

# WWDC 2026: Explore Advances in RealityKit

This session provides an in-depth look at the latest updates to **RealityKit**, Apple’s framework for building spatial experiences across VisionOS, iOS, iPadOS, macOS, and tvOS. The presentation focuses on elevating the visual and auditory fidelity of spatial apps through advanced lighting, physics simulations, and improved performance workflows, all of which are designed to integrate seamlessly with the new capabilities of **RealityComposer Pro 3**.

---

### Key Concepts and Features

*   **Advanced Lighting & Shadows:**
    *   **Lightmapping:** Integration with RealityComposer Pro 3’s *LightBaker* to generate static indirect lighting, ambient occlusion, and beauty maps for enhanced interior realism.
    *   **Soft Shadows:** New API support for area-based light sources. By adjusting the `shadow.lightSize` (in meters) and setting shadow quality to `medium` or `high`, developers can generate realistic penumbras.
    *   **Projective Textures:** Allows entities (like spotlights) to project textures (e.g., film patterns or caustic effects) onto surfaces.
    *   **Physical Space Lighting:** A feature that enables virtual light sources to interact with real-world geometry captured via the Scene Understanding Mesh.
*   **Navigation:**
    *   **Navigation Mesh:** A system for NPC pathfinding that supports area-specific traversal costs and off-mesh connections (e.g., bridges/ladders) to bridge disconnected mesh regions.
*   **Cloth Simulation:**
    *   Provides a particle-spring based system for high-fidelity fabric movement. Includes `ClothBodyComponent`, `ClothColliderComponent`, and `ClothSimulationComponent` for defining material properties like stiffness and friction.
*   **Gaussian Splats:**
    *   A high-performance technique to render volumetric, photorealistic captures of real-world objects using buffers for position, scale, rotation, opacity, and spherical harmonics.
*   **Immersive Audio:**
    *   Uses ray-traced geometrical acoustics. Developers can define `ReverbMesh` resources (using custom materials with specific absorption/scattering coefficients) to realistically model how sound bounces in virtual environments.

---

### Code Patterns and Techniques

*   **Dynamic LOD (Level of Detail):** Developers can register multiple entity versions for an object. RealityKit provides `AddByCameraDistance` and `AddByScreenArea` convenience functions to switch between detail levels automatically, optimizing GPU compute.
*   **Thermal Monitoring:** To ensure performance stability, developers are encouraged to observe the `ThermalStateDidChange` notification. If the device reaches "Serious" or "Critical" status, the app should adapt by lowering shadow quality or adjusting LOD thresholds.
*   **Kinematic Constraints in Cloth:** To keep cloth attached to objects (e.g., curtains on a rod), developers can mark specific vertices as `Kinematic`. Kinematic vertices ignore simulation forces and follow the transform of the entity, allowing for "pinning" functionality.
*   **NavMesh Querying:** The `NavigateEntityExtension` pattern uses an `async computePath` function, which returns an array of nodes. Developers must iterate through these to distinguish between standard navigation nodes and off-mesh connection nodes (like ladders).

---

### Practical Takeaways for Developers

1.  **Prioritize RealityComposer Pro 3:** Many of the complex features discussed (light baking, navigation mesh creation) are best handled within the updated RealityComposer Pro 3 environment to streamline workflows.
2.  **Performance First:** Always monitor thermal states. The session emphasizes that high-fidelity features like soft shadows and advanced cloth simulations are power-intensive; using LODs effectively is mandatory for a smooth user experience.
3.  **Real-World Integration:** Utilize `PhysicalSpaceLighting` and `RoomSense` (in shared spaces) to ensure virtual objects feel like they truly belong in the user's room.
4.  **Audio Design:** Don't neglect spatial acoustics. Using custom reverb materials based on the virtual environment’s geometry significantly increases immersion, especially on Apple Vision Pro.
5.  **Resources:** Apple provides specific samples for Gaussian Splat rendering and custom reverb meshes. Developers should download these from the **Apple Developer Portal** to examine the implementation of these APIs in production-ready contexts.

**Additional Mentioned Features:** The session briefly highlights upcoming capabilities, including coordinated multi-source audio, advanced character rendering (subsurface scattering/hair shaders), and customizable portal materials.

<!-- resources -->

---

## Resources

- [Gaussian splats on visionOS](https://developer.apple.com/documentation/visionOS/gaussian-splats-on-visionos)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/279/4/ab575725-be7d-4348-a3ae-6595ef4070c4/downloads/wwdc2026-279_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/279/4/ab575725-be7d-4348-a3ae-6595ef4070c4/downloads/wwdc2026-279_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/279/
