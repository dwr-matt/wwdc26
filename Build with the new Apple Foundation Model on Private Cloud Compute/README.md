# Build with the new Apple Foundation Model on Private Cloud Compute

# WWDC 2026: Build with the new Apple Foundation Model on Private Cloud Compute

## Session Overview
This session introduces the expansion of Apple’s Foundation Models framework to include **Private Cloud Compute (PCC)**. While previous years focused on on-device LLMs, this update allows developers to offload complex, resource-heavy AI tasks—such as processing large inputs, extensive tool calling, and advanced reasoning—to Apple’s secure server-side infrastructure. The integration is designed to be seamless, leveraging existing Swift APIs and providing a privacy-first, zero-configuration experience that does not require API keys or manual authentication.

---

## Key Concepts and Frameworks

### 1. Private Cloud Compute (PCC)
*   **Privacy-First:** Designed with end-to-end privacy; user data is processed for requests only and never stored.
*   **Zero-Auth Integration:** PCC is integrated directly into the OS and iCloud. If a user has an Apple Intelligence-compatible device, the model is available automatically without the need for API keys or account setup.
*   **Cost Model:** There are no token costs for developers. Usage is managed via the user's daily iCloud-linked quota, which can be extended via iCloud Plus.
*   **Availability:** Currently restricted to apps with fewer than 2 million downloads; developers must apply via the Apple Developer website.

### 2. The Foundation Models Framework
*   **Unified API:** The framework uses the same Swift-based API for both on-device and PCC models, allowing developers to switch between models by changing only one line of code.
*   **Capabilities:** PCC models support **32K context windows** (compared to 4K on-device) and advanced **reasoning capabilities**.

### 3. Reasoning Levels
The PCC model introduces three tiers of "reasoning," where the model generates internal thoughts before producing a final response:
*   **Light:** Gathers additional context.
*   **Moderate:** Deepens the logical process.
*   **Deep:** Generates an extensive reasoning segment (which can be longer than the final response).
*   *Note:* Reasoning tokens count toward the 32K context limit.

---

## Code Patterns & Techniques

### Switching Models
The framework allows for a unified approach to model management. You can toggle between local and server execution seamlessly:

```swift
// Switch from on-device to PCC by swapping the session initializer
let session = PrivateCloudComputeSession() // Or SystemLanguageModelSession()
session.respond(to: prompt, reasoningLevel: .deep) { response in
    // Handle response
}
```

### Handling Quotas & Limits
Developers are expected to handle usage limits gracefully rather than relying on system alerts.
*   **Detection:** Use the `quota` property on the model to check if `isLimitReached` or `isNearingLimit`.
*   **UX Pattern:** Instead of blocking the user with an alert, show a subtle label in the UI providing an actionable button to upgrade the iCloud quota.
*   **Testing:** Xcode now includes a debug option under **Scheme > Options > Simulate Apple Foundation Models Availability** to trigger `QuotaUsageLimitReached` or `NearingUsageLimit` states for testing.

---

## Practical Takeaways for Developers

1.  **Evaluate Before You Build:** Don't default to server-side models for everything. Use the new **Evaluations Framework** (introduced in a separate session) to test if your task can be solved by the on-device model, which is faster and works offline.
2.  **Graceful Degradation:** Always check the availability API before invoking the model. If Apple Intelligence is unavailable, ensure your app degrades gracefully.
3.  **Monitor Progress:** Because "Deep" reasoning can take time, observe the `transcript` object during the session. This allows you to show users the model’s "thought process" in real-time, improving the perceived latency.
4.  **Use the Right Tool for the Job:**
    *   **On-device:** Use for low latency, offline tasks, and simple inputs.
    *   **PCC:** Use for heavy reasoning, 32K context needs, or multi-step tool orchestration.
5.  **Context Management:** Use the `contextSize` property programmatically to ensure your inputs (including images and reasoning tokens) fit within the model's limitations before sending a request.

<!-- resources -->

---

## Resources

- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/319/4/1a3ac4f6-73d2-4a24-9e5d-0cfd56564f42/downloads/wwdc2026-319_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/319/4/1a3ac4f6-73d2-4a24-9e5d-0cfd56564f42/downloads/wwdc2026-319_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/319/
