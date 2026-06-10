# Make your game great with touch

## WWDC 2026 Session Summary: Make Your Game Great with Touch

### Overview
This session focuses on optimizing game experiences for iOS and iPadOS by implementing intuitive, high-performance touch controls. While many developers port existing titles to mobile using the Game Porting Toolkit, a direct mapping of physical controller inputs to on-screen overlays often leads to visual clutter and poor ergonomics. Kei Yu from the Game Technology team demonstrates how to use the **Touch Controller framework** to create adaptive, gesture-based, and context-aware touch interfaces that feel native to mobile devices.

---

### Key Concepts, APIs, and Frameworks

*   **Touch Controller Framework:** An extension of the existing `GameController` framework that provides specialized objects for touch input. It integrates directly with Metal for high-performance rendering and maps touch events to `GCController` objects, allowing developers to reuse existing game logic.
*   **Flexible Layouts:** Uses an anchor-based system (nine anchor points) to ensure controls scale gracefully across various device shapes and sizes.
*   **Safe Area Awareness:** Incorporates `UIKit` safe area insets to ensure UI elements do not conflict with hardware features like the Dynamic Island or rounded corners.
*   **Dynamic Controls:** Unlike static controller buttons, touch controls can be context-aware—changing icons based on gameplay states (e.g., swapping a "strike" icon for a "fireball" icon) or hiding entirely when not in use.

---

### Code Patterns and Techniques

*   **Controller Integration:** 
    *   Initialize a `TCTouchController` from a descriptor and enable it via the `Connect` API.
    *   Override `touchesBegan`, `touchesMoved`, and `touchesEnded` in `UIView` to feed input into the touch controller, which then reports state changes through the `GCController` pattern.
*   **Adaptive Rendering:** Use the `TCTouchController` Render API to draw controls, utilizing a layer-based system (`TCControlContents`) to stack visual elements (like a glowing halo for sprinting).
*   **Gesture-Based Input:**
    *   **Thumbstick Embedding:** Instead of requiring a separate "Sprint" button, detect movement magnitude on the thumbstick to trigger sprinting.
    *   **Touchpad Integration (`TCTouchPad`):** Replace the right thumbstick with a full-screen or half-screen region to provide latency-free camera control.
    *   **Hold-and-Drag:** Implement complex maneuvers (like aiming) by tracking raw touch deltas in the `touchesMoved` handler while a button is in a "pressed" state.

---

### Practical Takeaways for Developers

1.  **Don't Just "Skin" a Controller:** A one-to-one mapping of a physical controller to the screen is rarely optimal. Prioritize screen real estate by removing unused buttons and merging complex combinations into single, multi-functional touch actions.
2.  **Design for "Full-Screen" Interaction:** Utilize the entire screen for primary inputs. Thumbsticks should have large hit areas (using `colliderShape`), and camera movement should be handled via a "touchpad" rather than a visual stick, which eliminates over-rotation and visual obstruction.
3.  **Context Matters:** Use the `isDisplayed` or `isEnabled` properties to hide controls that are not relevant to the current player state (e.g., hiding the "Pick Up" button unless near an item). 
4.  **Provide Visual Feedback:** Touch lacks the tactile feedback of a physical controller. Use visual indicators—like glows or halos—to represent active states (sprinting, power selection, or interaction readiness) so the player remains informed without reading text.
5.  **Use the Framework for Performance:** By building on the `GameController` framework, your game remains compatible with physical hardware while gaining a polished, performant touch layer that feels native to iOS.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/358/4/fdd21d54-a233-49d4-8d00-4dc51284515d/downloads/wwdc2026-358_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/358/4/fdd21d54-a233-49d4-8d00-4dc51284515d/downloads/wwdc2026-358_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/358/
