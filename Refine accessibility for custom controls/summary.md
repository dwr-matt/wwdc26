# Refine accessibility for custom controls

# WWDC 2026: Refine accessibility for custom controls

This session provides a roadmap for making custom SwiftUI controls fully accessible. It emphasizes that while custom UI elements allow for unique interactions, they must be explicitly configured to provide the same level of utility to users of assistive technologies (like VoiceOver, Switch Control, and Voice Control) that they provide to sighted users. The session focuses on ensuring users can understand a control’s purpose, its current state, how to interact with it, and how to receive feedback.

---

### Key Concepts
*   **Accessibility Guiding Principles:** Every custom control should clearly define:
    *   **Purpose:** What the control does (via label).
    *   **Value:** Its current status or state (via value).
    *   **Actions:** How to manipulate the control (via traits and actions).
    *   **Feedback:** Real-time updates during interaction.
*   **Interaction Models:**
    *   **Adjustable:** Best for linear inputs (sliders, steppers) that map well to swipe-up/down gestures.
    *   **Pass-Through Gesture:** A technique where a user double-taps and holds to send touch events directly to a control for precise, fine-grained adjustments.
    *   **Custom Actions:** Used for complex controls (e.g., a 2D pad) where simple increment/decrement is insufficient.
    *   **Direct Touch:** An API that routes raw touch events to the control, allowing complex multi-gesture support (e.g., pinching or tapping).

---

### APIs and Frameworks
*   **SwiftUI Accessibility Modifiers:**
    *   `accessibilityLabel(_:)`: Assigns a descriptive name to the control.
    *   `accessibilityValue(_:)`: Reports the current state (e.g., "50%").
    *   `accessibilityAddTraits(.isAdjustable)`: Informs assistive tech that the element supports increment/decrement.
    *   `accessibilityAdjustableAction { ... }`: Defines the logic for how the control reacts to swipe gestures.
    *   `accessibilityAction(named: "Label") { ... }`: Registers custom actions that appear in the VoiceOver rotor.
    *   `accessibilityDirectTouch(options:)`: Enables direct interaction. Options include:
        *   `requireActivation`: Prevents accidental activation until a double-tap.
        *   `silentOnTouch`: Mutes VoiceOver output to prevent speech from conflicting with custom audio feedback.

---

### Code Patterns & Techniques
*   **Effective Feedback Loop:** When using pass-through gestures, do not announce every minor change to avoid "noise." Use a throttle: only announce if the value changes significantly and at least 0.3 seconds have passed since the last announcement.
*   **2D Navigation:** For multi-axis controls (like an equalizer pad), avoid the `adjustable` trait. Instead, implement four distinct `accessibilityAction` blocks (Move Up/Down/Left/Right) to allow users to navigate the space incrementally.
*   **Activation Points:** Use the `accessibilityActivationPoint` property to ensure gestures (like pass-through) initiate at the current value of a slider, providing a better user experience for fine-tuning.

---

### Practical Takeaways for Developers
1.  **Start with Audit:** Enable VoiceOver on your device and navigate your own app. If the control is simply announced as an "image" or "button" without context, it needs accessibility attention.
2.  **Use `isAdjustable` for Simplicity:** If your custom control behaves like a slider, the `adjustable` trait is the most standard and expected way to handle it.
3.  **Prioritize Custom Actions over Direct Touch:** Direct touch can be difficult for some users. Whenever possible, provide equivalent `custom actions` as a secondary way to perform the same task.
4.  **Balance Feedback:** Be mindful of "speech fatigue." If your control provides its own sound cues, use `silentOnTouch` so the user isn't bombarded by both VoiceOver and your app’s native audio.
5.  **Test for "Everywhere":** Remember that accessibility improvements for VoiceOver often benefit Switch Control and Voice Control users automatically when implemented using these standard Apple APIs.

<!-- resources -->

---

## Resources

- [Accessible controls](https://developer.apple.com/documentation/SwiftUI/Accessible-controls)
- [Accessible descriptions](https://developer.apple.com/documentation/SwiftUI/Accessible-descriptions)
- [Accessibility fundamentals](https://developer.apple.com/documentation/SwiftUI/Accessibility-fundamentals)
- [Creating accessible views](https://developer.apple.com/documentation/SwiftUI/creating-accessible-views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/220/4/945f8d34-8427-4476-ae75-34edc4a9c3f9/downloads/wwdc2026-220_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/220/4/945f8d34-8427-4476-ae75-34edc4a9c3f9/downloads/wwdc2026-220_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/220/
