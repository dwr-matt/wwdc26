# Secure your apps with App Attest

# Summary: Secure Your Apps with App Attest (WWDC 2026)

## Overview
This session introduces updated strategies for using **App Attest** to protect applications against unauthorized modification, reverse engineering, and fraudulent activity. As fraudsters increasingly target apps by modifying code, re-signing binaries, or injecting cheat menus, App Attest provides a robust, hardware-backed mechanism to ensure that your server is only communicating with a genuine, unmodified version of your app running on authentic Apple hardware. The session details how to integrate attestation and assertion workflows, manage keys securely, and utilize the new "fraud metric" to perform risk assessments.

---

## Key Concepts and APIs

### 1. The App Attest Core Workflow
*   **Key Generation:** App Attest creates a key pair inside the device's **Secure Enclave**. The app stores the key ID in the Keychain. These keys are per-device and per-app, surviving app updates but not reinstalls or device restores.
*   **Attestation:** The app requests an attestation for a key ID. Apple’s servers validate the hardware environment and provide cryptographic proof. Your server must validate this attestation (signature, certificate chain, and receipt) to establish trust.
*   **Assertions:** Used to sign ongoing payloads to your server. Assertions ensure the data in transit has not been tampered with and utilize a **strictly increasing counter** to prevent replay attacks.

### 2. New Features (iOS 27 / macOS 27+)
*   **Launch Validation Category:** Surfaces information on how the app was launched (e.g., App Store vs. TestFlight), helping identify if a sideloaded or modified version is running.
*   **Bundle Version Tracking:** Allows servers to verify that the app version reporting is indeed the one you distributed.
*   **macOS Security Policies:** App Attest keys on macOS now enforce "Full Security Mode" and "System Integrity Protection." The **ACL blob OID** in the leaf certificate allows your server to verify these security conditions were active when the attestation was generated.
*   **Extensions:** New structures in the authenticator data for both attestation and assertion, containing the launch category and bundle version metadata.

### 3. The Fraud Metric
*   A tool that returns the count of unique attested keys associated with a device over the past 30 days.
*   **Purpose:** Not a "block" signal, but a diagnostic tool. High counts suggest a device might be acting as a "broker" to generate valid attestations for multiple modified instances of an app.

---

## Code Patterns and Techniques

*   **Server-Controlled Initiation:** Your server should dictate when an attestation occurs to prevent hitting rate limits and to stay within a safe "requests-per-second" bound.
*   **Background Tasks:** Always perform attestation operations in a background task to keep the UI responsive and minimize user friction.
*   **Exponential Backoff:** Avoid hard-coded retry logic; implement exponential backoff to protect against spikes in traffic to Apple’s attestation servers.
*   **Validation Logic:** 
    *   **Server-Side Only:** Never trust the app to validate its own attestation. 
    *   **Counter Monitoring:** Always verify that the assertion counter increases with every request. A steady or decreasing counter is a sign of a compromised client.
*   **Graceful Degradation:** If an attestation fails or seems suspicious, do not immediately block the user. Instead, degrade functionality or trigger "heightened monitoring" to avoid punishing legitimate users who may have simply reinstalled the app.

---

## Practical Takeaways for Developers

1.  **Rebuild with Latest SDKs:** Ensure your app is built against the latest SDK to access the new `Is Supported` API and the expanded extension metadata.
2.  **Audit Your Sensitive Flows:** Apply App Attest primarily to high-risk areas, such as authentication flows, premium content delivery, and leaderboard submissions.
3.  **Handle Key Rotation:** Recognize that legitimate actions (like device restores or app reinstalls) will cause key rotation. Do not invalidate old keys immediately; keep a map of attestations per user.
4.  **Implement Comprehensive Risk Assessment:** Integrate the `Is Supported` API response, launch validation categories, and the fraud metric into your backend’s decision-making engine.
5.  **Use the Keychain Correctly:** Store key IDs in the system Keychain to ensure persistence across app launches, but remember they do not sync across devices.
6.  **Avoid Blocking via Fraud Metric:** Use the fraud metric as a signal for investigation rather than an automated block mechanism to protect against false positives.

<!-- resources -->

---

## Resources

- [W3C Authenticator Data](https://www.w3.org/TR/webauthn-3/#sctn-authenticator-data)
- [About System Integrity Protection on your Mac](https://support.apple.com/en-us/102149)
- [DeviceCheck](https://developer.apple.com/documentation/DeviceCheck)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/201/4/d3eb2e5b-5104-4aee-a754-9985008a5b06/downloads/wwdc2026-201_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/201/4/d3eb2e5b-5104-4aee-a754-9985008a5b06/downloads/wwdc2026-201_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/201/
