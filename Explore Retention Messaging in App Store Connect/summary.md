# Explore Retention Messaging in App Store Connect

# Summary: Explore Retention Messaging in App Store Connect

## Session Overview
This session introduces **Retention Messaging**, a new feature designed to help developers reduce churn by engaging users at the precise moment they attempt to cancel an auto-renewable subscription. By presenting value propositions, visual assets, or promotional offers directly within the App Store’s subscription cancellation flow, developers can improve their save rates. The session covers two primary implementation strategies: the low-code **App Store Connect (ASC)** approach and the more advanced **Real-Time Retention Messaging** via a server-to-server API.

---

## Key Concepts and Frameworks

### 1. Retention Messaging (App Store Connect)
This is a configuration-based approach suitable for developers who want a straightforward way to display messaging without managing server infrastructure.
*   **Functionality:** You define message text, optional images, and optional subscription offers within ASC.
*   **Views:** Supports three formats: Message only, Message + Image, or Message + Offer.
*   **Automation:** If multiple offers are assigned, the App Store intelligently selects the best offer for which the specific customer is eligible.

### 2. Real-Time Retention Messaging
A more granular, dynamic approach that allows developers to decide what a user sees on a per-request basis.
*   **Server-to-Server:** The App Store sends an HTTP request to your endpoint when a user attempts to cancel. Your server responds with the specific messaging, offer, or plan change you wish to display.
*   **Switch Plans:** Unlike the ASC version, this allows you to offer an alternative product (e.g., switching from a monthly to an annual plan) within the same subscription group.
*   **Fallback Logic:** It is critical to set up ASC-based messaging even if you use Real-Time Messaging; the App Store uses your ASC configuration as a fallback if your server is unreachable or responds with malformed data.

---

## Technical Implementation and Techniques

### Integration Workflow
*   **Sandbox Testing:** Both methods are fully testable in the Sandbox environment. 
*   **Performance Requirements:** Real-time implementations require a performance test in Sandbox to ensure your server can respond quickly enough to satisfy the App Store’s latency requirements.
*   **API Usage:** 
    *   **Retention Messaging API:** Used for setting up real-time endpoint URLs, managing images, and initiating performance tests.
    *   **App Store Connect API:** Used for programmatic setup of the non-real-time ASC messaging flow.

### Data Patterns
*   **Signed Transactions:** When a retention offer is redeemed, the `signedTransaction` and `renewalInfo` fields will be updated with a `new offer type value of 5`.
*   **Request/Response Fields:** Real-time requests include the `originalTransactionID` and customer `locale` to enable personalized, localized responses. 
*   **Response Options:** When the App Store queries your endpoint, you can respond with one of three formats:
    1.  **Message/Image:** A standard communication.
    2.  **Alternate Product:** A "switch plan" offer.
    3.  **Promotional Offer:** Providing a specific discount (e.g., three months free), which requires a promotional offer signature.

---

## Practical Takeaways for Developers

*   **Impact on Metrics:** Adopting these tools has shown significant potential for increasing subscriber retention. Promotional offers, in particular, saw an observed save rate increase of up to 223% (or +5.5 points) in the presented case study.
*   **Choosing the Right Method:**
    *   Use **App Store Connect Retention Messaging** if you lack server infrastructure or prefer the App Store to manage the presentation logic.
    *   Use **Real-Time Retention Messaging** if you need to perform custom eligibility logic, want to offer "switch plans," or require fine-tuned control over the customer experience.
*   **Action Items:**
    *   **Start with ASC:** Begin by configuring messaging in App Store Connect to see immediate benefits.
    *   **Apply for Access:** Real-time implementation is powerful but requires access; developers should fill out the interest form linked in the session resources.
    *   **Always Provide Fallbacks:** Regardless of which method you choose, treat your ASC configuration as the primary safety net for customer communication.

<!-- resources -->

---

## Resources

- [Interest form: Real-time Retention Messaging](https://developer.apple.com/contact/request/retention-messaging-api/)
- [Supporting monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/supporting-monthly-subscriptions-with-a-12-month-commitment)
- [Retention Messaging API](https://developer.apple.com/documentation/RetentionMessaging)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/309/4/afa0aec8-f216-43ed-bcb1-1a3742e49dac/downloads/wwdc2026-309_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/309/4/afa0aec8-f216-43ed-bcb1-1a3742e49dac/downloads/wwdc2026-309_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/309/
