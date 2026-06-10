# What's new in managing Apple devices

# WWDC 2026: What’s New in Managing Apple Devices

## Overview
This session provides a comprehensive update on Apple’s device management ecosystem, emphasizing that **Declarative Management** is no longer a roadmap item but the established industry standard. The presentation covers major advancements across Apple Business and School Manager, expanded declarative capabilities for macOS 27 and iOS/iPadOS, new identity management workflows with Platform SSO, and specific enhancements for education environments. The primary goal is to help IT administrators and developers build more secure, automated, and user-friendly management solutions.

---

## Key Concepts, APIs, and Frameworks

### 1. Apple Services & API Expansion
*   **Apple Business Platform:** A new all-in-one hub for organizations of all sizes, now available in over 200 countries.
*   **New APIs:** Introduced for automation of blueprints, configuration management, user/group modification, app licensing, and auditing.
*   **Subscription Licensing:** IT admins can now purchase and manage App Store subscriptions at scale using existing device management distribution workflows.

### 2. Declarative Management Advancements
*   **Managed Migration:** A new declarative configuration allows IT to facilitate seamless migrations to new Macs while preserving enrollment state and specific security settings.
*   **Apple Intelligence & Siri:** Granular declarative controls are now available to manage specific AI features and Siri settings.
*   **Status Channel:** Expanded to include real-time reporting on device state (e.g., Lockdown Mode, Push Tokens, Shared iPad status).
*   **Device System Health:** Devices can now report hardware issues (Baseband, Camera, FaceID, TouchID) via the status channel, enabling proactive fleet maintenance.
*   **Enhanced Log Collection:** A new `Trigger Enhanced Log Collection` command allows IT to remotely initiate diagnostic log gathering for AppleCare analysis.
*   **Content Caching:** New declarative configurations and status items allow for centralized monitoring and management of local content caching servers.

### 3. App & Privacy Management
*   **Managed Apps for macOS:** The declarative app configuration framework (previously iOS/VisionOS) is now available on macOS 27, supporting hardware-bound keys and managed device attestation.
*   **Binary Execution Control:** New settings leverage the **Endpoint Security Framework** to allow or deny binaries based on code-signing properties.
*   **Consolidated Privacy Consent:** A unified prompt replaces multiple alerts for apps and Safari websites, allowing IT to provide justification strings and steer users toward "Allow" (the default choice).

### 4. Identity Management (Platform SSO)
*   **Web-based Auth:** macOS 27 introduces a secure, system-managed web view in the login window and Screen Unlock to support modern authentication flows (multi-factor, QR codes, custom challenges).
*   **Touch ID Enforcement:** IT can now mandate Touch ID as a second factor for login, Screen Unlock, and FileVault unlock.
*   **Authenticated Guest Mode:** Extended to support unlocking FileVault-protected Macs, ensuring data remains encrypted while allowing temporary access for shared workflows (e.g., healthcare).

### 5. Education Features
*   **Shared iPad:** Adds support for Authenticated Guest Mode, allowing temporary sessions that automatically wipe data upon sign-out.
*   **Guided Browsing:** A new feature in the **Classroom app** allowing teachers to restrict student browsers to specific tabs or websites with granular control over camera/microphone permissions.

---

## Code Patterns & Techniques
*   **Many-to-Many Credential Mapping:** Declarative management now supports referencing a single credential (certificate/identity/password) across multiple configurations, drastically increasing efficiency and reducing the size of configuration profiles.
*   **Status Channel Subscriptions:** Rather than polling for device state, developers should subscribe to specific status items. The device will automatically push updates to the server as values change.
*   **Isolated Web Authentication:** When using the new web-based SSO, the camera process is strictly isolated from the web view. The web view only receives the decoded string from a QR code, ensuring the web page never accesses raw image data or user identity.

---

## Practical Takeaways for Developers
*   **Adopt Declarative Management:** If your product still relies on legacy configuration profiles, migrate to declarative configurations to improve performance and reliability.
*   **Leverage Managed App Framework:** Encourage enterprise developers to adopt the managed app framework to ensure their apps are compatible with secure, hardware-bound enterprise configurations.
*   **Build for Privacy:** Utilize the new consolidated privacy consent prompts to improve the UX for end-users while ensuring compliance with organizational policies.
*   **Documentation:** Review the complete device management object schema and documentation available on [GitHub](https://github.com) and [developer.apple.com](https://developer.apple.com).
*   **Next Steps:** Check out the supplementary sessions: *“Offer Subscriptions to Groups and Organizations,”* *“App-A-Test,”* and the *“Assessment Mode”* video for specific implementation details.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/206/4/e49f983e-700d-4d52-ae6b-a0fa1ea89fd0/downloads/wwdc2026-206_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/206/4/e49f983e-700d-4d52-ae6b-a0fa1ea89fd0/downloads/wwdc2026-206_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/206/
