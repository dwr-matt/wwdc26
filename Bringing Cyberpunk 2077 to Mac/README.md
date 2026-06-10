# Bringing Cyberpunk 2077 to Mac

# WWDC 2026 Session Summary: Bringing *Cyberpunk 2077* to Mac

## Overview
This session features Garrett Austin (Apple) and Pavel Sosko (CD Projekt Red) discussing the technical journey of porting *Cyberpunk 2077* to Apple Silicon. The talk details the transition from initial evaluation using the Game Porting Toolkit to the creation of a fully native macOS build. The presenters emphasize balancing the high graphical and computational demands of an open-world RPG with the unique platform requirements of macOS, resulting in an experience that feels native, performant, and optimized for a wide range of Apple devices.

## Key Concepts, APIs, and Frameworks
*   **Game Porting Toolkit (GPTK):** Used in the early stages to evaluate the Windows build in a translated environment. This provided data on performance bottlenecks (CPU vs. GPU) without requiring code changes.
*   **Metal & Metal Shader Converter:** The foundation of the native rendering pipeline. Metal Shader Converter was used to automate the transition of complex shader stacks to the Metal ecosystem.
*   **MetalFX Upscaling:** Utilized to provide performance headroom in graphically demanding scenes while maintaining high fidelity.
*   **"For This Mac" Preset:** A custom, hardware-aware graphics preset system that auto-detects GPU capabilities to configure optimal settings (resolution, V-Sync, HDR) upon the first launch.
*   **Extended Dynamic Range (EDR):** Apple’s API for HDR presentation. The team utilized it for automatic calibration, detecting display capabilities via EDR headroom to adjust tone mapping dynamically.
*   **Spatial Audio:** Integration of Apple’s spatial audio APIs via `AVAudioEngine`, specifically enabling head-tracked audio for AirPods.
*   **Game Mode:** Leveraged for higher CPU/GPU priority and reduced input latency for wireless controllers and AirPods.
*   **App Lifecycle Notifications:** Use of `NSNotificationCenter` events (e.g., `window-did-change-occlusion-state`, `application-did-change-screen-parameters`) to ensure the game behaves correctly during multitasking or display changes.

## Code Patterns & Techniques
*   **Performance Evaluation Loop:** The team used a consistent "hotspot" testing methodology, analyzing data from three sources:
    1.  In-engine profilers (statistical frame time).
    2.  **MetalHUD** (correlating gameplay events with rendering data).
    3.  Internal engine thread profiling (identifying CPU-bound systems like physics or audio).
*   **Resource Management:** To ensure a "native" feel, the engine reduces CPU/GPU activity when the game window is not in focus by listening to occlusion state notifications.
*   **Cursor Management:** Using `window-did-resign-key` and `window-did-become-key` notifications to toggle between the game’s custom cursor and the system cursor, ensuring a smooth experience when interacting with overlays.
*   **Dynamic Calibration:** The code queries the maximum potential EDR color component value. If the value exceeds 2.0, the game automatically enables HDR, removing the need for manual user calibration.

## Practical Takeaways for Developers
1.  **Start with Evaluation:** Before committing to a full port, use the Game Porting Toolkit to identify where your engine’s frame time is being spent. This saves time by highlighting whether you have a CPU, GPU, or architectural bottleneck early.
2.  **Automate Shader Conversion:** Integrate the Metal Shader Converter directly into your build pipeline. Treat shader conversion as a continuous loop: convert, validate against reference scenes, and refine edge cases.
3.  **Prioritize "First Launch" Quality:** Users appreciate an experience that "just works." Implement hardware detection early to provide sensible defaults for your target hardware.
4.  **Embrace System Integration:** Don't treat the game as an isolated process. By observing system-level notifications (display changes, window focus, controller connection), you make your game feel like a high-quality, professional application rather than a simple port.
5.  **Leverage Platform Features:** Use Apple-specific technologies like Game Mode and EDR to extract performance and visual fidelity that generic PC ports might miss.

<!-- resources -->

---

## Resources

- [Performing your own tone mapping](https://developer.apple.com/documentation/Metal/performing-your-own-tone-mapping)
- [Personalizing spatial audio in your app](https://developer.apple.com/documentation/PHASE/personalizing-spatial-audio-in-your-app)
- [Download the Game Porting Toolkit](https://developer.apple.com/games/game-porting-toolkit/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/356/5/d3ce460b-554d-4760-ae03-072c5acf42aa/downloads/wwdc2026-356_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/356/5/d3ce460b-554d-4760-ae03-072c5acf42aa/downloads/wwdc2026-356_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/356/
