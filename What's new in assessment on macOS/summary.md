# What's new in assessment on macOS

# Session Summary: What’s New in Assessment on macOS (WWDC 2026)

This session introduces the latest enhancements to the **Automatic Assessment Configuration (AAC) Framework** on macOS. The framework is designed for developers building secure, lockdown-based testing environments for education and certification. Chris, an engineer from the Education Technologies team, details how to create a hardened, tamper-resistant environment while maintaining equitable access for students through system-level customizations and process management.

---

### Key Concepts, APIs, and Frameworks
The session centers on the **`AEAssessmentConfiguration`** object, which serves as the primary interface for defining the parameters of an assessment session. To use these features, developers must request the restricted **Automatic Assessment Configuration entitlement** via the Apple Developer Portal.

Core functional areas covered include:
*   **System Preconditions:** Validating device health before an exam begins (e.g., ensuring the device is MDM-enrolled, SIP is enabled, or that only a single standard user is logged in).
*   **Accessibility Management:** Ensuring that assistive technologies remain available for students who need them, while allowing developers to restrict specific features that might conflict with test integrity.
*   **System Experience Customization:** Tailoring the UI to minimize distractions and prevent cheating. This includes toggling the visibility of the menu bar, specific menu extras (e.g., Wi-Fi, volume), the Dock, and the Apple menu.
*   **Input Method Restrictions:** Programmatically disabling features like Dictation, Emoji Pickers, Structural Input, and Autofill to prevent unauthorized assistance during exams.
*   **Process and File System Lockdown:** Restricting background processes and limiting file system access to specific directories using the Finder participant model.

---

### Code Patterns and Techniques
The session emphasizes using the `AEAssessmentConfiguration` object to set properties that dictate the behavior of the system during an active assessment:

*   **Configuring Preconditions:** Properties on the `AEAssessmentConfiguration` object allow developers to enforce a "hardened" state. If a device fails these checks, the system automatically alerts the user.
*   **Restricting Features:** To disable a feature (like Switch Control or Dictation), developers set the corresponding Boolean property to `false`. 
*   **Allow-listing:** The framework uses an "allow-list" approach. For example, setting `allowsDock = true` displays the dock, but only for apps explicitly identified as participants.
*   **Transition Callbacks:** Developers should **not** assume an assessment has started the moment the API is called. Instead, the session emphasizes registering for **framework transition callbacks** to drive app state changes based on when the system confirms the session has officially begun, ended, or been terminated.

---

### Practical Takeaways for Developers

*   **Adopt the Framework, Don't Build Alternatives:** Avoid trying to manually kill processes or block system features. Relying on the AAC framework ensures cleaner, more reliable behavior that is supported across macOS releases.
*   **Start Permissive, Tighten Deliberately:** Do not over-restrict. Only disable the features absolutely necessary for your specific assessment's security requirements to maintain a positive user experience.
*   **Accessibility First:** Design for accessibility from the start. Treat assistive technologies as requirements rather than "exceptions" that need to be carved out later.
*   **Test on Betas:** Because this framework involves deep system integration, regressions can occur with new macOS versions. Developers must test their full assessment matrix against macOS betas as soon as they are released to avoid production issues.
*   **Manage Participants Explicitly:** If your app needs to interact with files, you must add the `Finder` as a participant and explicitly define allowed directories for `open` and `save` operations.

<!-- resources -->

---

## Resources

- [Automatic Assessment Configuration](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/230/4/03914f48-0bbe-4f2d-bb09-3ae676579cf2/downloads/wwdc2026-230_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/230/4/03914f48-0bbe-4f2d-bb09-3ae676579cf2/downloads/wwdc2026-230_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/230/
