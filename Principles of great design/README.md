# Principles of great design

## Session Summary: Principles of Great Design (WWDC 2026)

### Overview
This session outlines Apple’s core design philosophy for developers building on their platforms. The speakers, Linda and Doug, argue that design is not merely about aesthetics or behavior, but about **making choices with intention**. By focusing on seven foundational pillars, developers can create experiences that are not only functional but also respectful of users’ time, trust, and well-being. The session emphasizes that there is no singular formula for perfection; instead, developers must use their intuition to balance these principles to create software that feels purposeful, intuitive, and high-quality.

---

### Key Design Principles
Apple identifies seven principles that should guide every stage of development, from the initial sketch to the final line of code:

1.  **Purpose:** Build with clear intent. Every feature consumes a user's time and trust; avoid adding unnecessary features by focusing on what provides genuine value.
2.  **Agency:** Give users control. Allow them to navigate your app at their own pace rather than forcing a predetermined path. Provide **forgiveness** (e.g., undo/redo) to allow for safe exploration.
3.  **Responsibility:** Act in the user’s best interest. This includes prioritizing privacy (only ask for data when necessary and in context) and ensuring safety (mitigating risks, especially when integrating AI).
4.  **Familiarity:** Build on what users already know. Utilize standard metaphors (e.g., a trash icon for deletion) and remain consistent with patterns and placement across your app and the platform.
5.  **Flexibility:** Design for a diverse audience and multiple contexts. Adapt your interface to different devices (iPhone vs. Mac), environments (e.g., hands-free use), and accessibility needs.
6.  **Simplicity:** Strip away the unnecessary. Simplicity is not just minimalism; it is clarity and conciseness. Use plain language and strong visual hierarchy to help users find exactly what they need.
7.  **Craft:** Demonstrate care through attention to detail. High-quality typography, responsive animations, and robust performance inspire confidence and trust.

---

### Key Concepts & Techniques
*   **The "Permission" Trap:** Avoid triggering system prompts for personal data immediately upon launch. Wait until the user understands the context and value of the feature.
*   **AI Safeguarding:** When implementing AI, proactively anticipate potential failures. Use confirmations and disclaimers, and be prepared to remove high-risk features if they cannot be made safe.
*   **Visual Hierarchy:** Use order, spacing, and contrast to ensure the most important UI element is always the most obvious.
*   **Maintenance as Craft:** Great design is not "set it and forget it." As hardware and software features evolve, update your app to remain relevant and supported.

---

### Practical Takeaways for Developers
*   **Adopt the "Undo" Mindset:** Providing an easy way to recover from mistakes significantly increases user confidence and encourages deeper exploration of your application.
*   **Audit Your Interface:** Periodically review your UI to see if information can be distilled or if complex data should be visualized as a graphic.
*   **Use the HIG:** The **Human Interface Guidelines (HIG)** are the primary source for these patterns. A new "Design Principles" page has been added to the documentation to provide further guidance.
*   **Avoid Reinventing the Wheel:** For common actions, use the metaphors and patterns users are already familiar with from other apps and the OS.
*   **Design for the Device:** A mobile experience and a desktop experience should not be identical. Leverage the unique input methods (touch vs. precise pointer) and capabilities of each platform.
*   **Embrace "Delight" as an Outcome:** Delight is not an additive (like adding confetti); it is the natural byproduct of successfully executing all other design principles with care.

---

*For further learning, developers are encouraged to visit the [Human Interface Guidelines (HIG)](https://developer.apple.com/design/human-interface-guidelines/) on the Apple Developer website.*

<!-- resources -->

---

## Resources

- [Human Interface Guidelines: Design principles](https://developer.apple.com/design/human-interface-guidelines/design-principles)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/250/4/ad804f32-2805-48aa-891c-8c742579acab/downloads/wwdc2026-250_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/250/4/ad804f32-2805-48aa-891c-8c742579acab/downloads/wwdc2026-250_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/250/
