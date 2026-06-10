# What's new in Apple In-App Purchase

# WWDC 2026: What's new in Apple In-App Purchase

## Session Overview
This session provides an update on the Apple Commerce platform, focusing on new pricing strategies, enhanced merchandising capabilities, and streamlined submission workflows for In-App Purchases (IAP). The primary highlight is the introduction of a "monthly billing with a 12-month commitment" model for annual subscriptions. Additionally, the session covers improvements to the Offer Code Redemption API, the upcoming introduction of subscription bundles and suites, and a unified, more efficient App Store Connect submission process.

---

## Key Concepts, APIs, and Frameworks

### 1. Monthly Subscriptions with 12-Month Commitment
*   **Concept:** Allows developers to offer an annual subscription payable in 12 monthly installments.
*   **Pricing Terms:** A new property on `SubscriptionInfo` that provides an array of available billing plans. 
*   **StoreKit Views:** Updated to automatically handle the display of these new billing options without requiring manual UI reconstruction.
*   **Server-side:** New fields are available in JWS transaction objects and `RenewalInfo` to track commitment progress, price, and expiration.

### 2. Subscription Bundles and Suites
*   **Bundles:** A collection of individual subscriptions sold together at a discount.
*   **Suites:** A group of subscriptions that exist only as part of the suite and cannot be purchased individually.
*   **Availability:** Testing begins in Xcode 27; further details are expected later in 2026.

### 3. Offer Code Redemption API
*   **Enhancements:** The API now returns a `VerificationResult` upon completion, providing either a `Transaction` object on success or an error description on failure.
*   **Customization:** Supports `RedeemOption` values to configure the redemption flow.

### 4. Enhanced App Store Connect Submission
*   **Unified Review:** Developers can now group multiple IAPs, in-app events, and custom product pages into a single `Review Submission`.
*   **API Updates:** The App Store Connect API is being updated to support `Review Submissions` and `Review Submission Items`, while older individual IAP/Subscription resources are being deprecated.

---

## Code Patterns and Techniques

*   **Merchandising Billing Plans (SwiftUI):**
    Developers should use the `preferredSubscriptionPricingTerms` view modifier on `SubscriptionStoreView`.
    ```swift
    // Example pattern for filtering plans
    SubscriptionStoreView(groupID: "...")
        .preferredSubscriptionPricingTerms { terms in
            terms.filter { $0.type == .monthly }
        }
    ```
*   **Managing Purchases:**
    When initiating a purchase, pass the specific `BillingPlanType` as a purchase option to ensure the user is billed according to the selected commitment model.
*   **Handling Commitment Data:**
    Use the `latestTransaction` property to retrieve `commitmentInfo`. This is essential for unlocking content and verifying if a user is within an active 12-month commitment period.
*   **Xcode Testing:**
    Configure these new models in the StoreKit Configuration file (Xcode 26.5+) by selecting an annual subscription and using the "billing plan picker" to define the monthly commitment parameters.

---

## Practical Takeaways for Developers

1.  **Adopt the New Pricing Model:** If you have annual subscriptions, evaluate if offering a monthly installment plan can help expand your customer base by reducing the barrier to entry.
2.  **Migrate to the New Review API:** Since the legacy IAP and Subscription resources in the App Store Connect API are being deprecated, begin migrating your automated submission scripts to the `Review Submissions` API as soon as possible.
3.  **Update Redemption Flow:** If your app uses Offer Codes, update your `OfferCodeRedemption` implementation to handle the new `VerificationResult` return type, ensuring better error handling and UX.
4.  **Leverage StoreKit Views:** Rely on StoreKit’s native SwiftUI views for merchandising to ensure your store UI automatically stays compliant and visually consistent across all Apple platforms (iOS, macOS, visionOS, etc.).
5.  **Preparation:** Download the sample `SK Demo` project provided in the session resources to experiment with the new `BillingPlanType` configuration in a local sandbox environment.

<!-- resources -->

---

## Resources

- [In-App Purchase types](https://developer.apple.com/help/app-store-connect/reference/in-app-purchases-and-subscriptions/in-app-purchase-types)
- [Managing the life cycle of monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/managing-lifecycle-of-monthly-subscriptions-with-a-12-month-commitment-)
- [Supporting monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/supporting-monthly-subscriptions-with-a-12-month-commitment)
- [App Store Server Notifications V2](https://developer.apple.com/documentation/AppStoreServerNotifications/App-Store-Server-Notifications-V2)
- [Supporting offer codes in your app](https://developer.apple.com/documentation/StoreKit/supporting-offer-codes-in-your-app)
- [Implementing a store in your app using the StoreKit API](https://developer.apple.com/documentation/StoreKit/implementing-a-store-in-your-app-using-the-storekit-api)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/210/4/f029ab19-6670-48c6-b9b1-88ac6692cdda/downloads/wwdc2026-210_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/210/4/f029ab19-6670-48c6-b9b1-88ac6692cdda/downloads/wwdc2026-210_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/210/
