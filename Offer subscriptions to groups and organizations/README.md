# Offer subscriptions to groups and organizations

# WWDC 2026: Offer Subscriptions to Groups and Organizations

## Session Overview
This session introduces new capabilities for developers to sell auto-renewable subscriptions to groups, teams, and organizations. The goal is to move beyond individual consumer subscriptions by allowing users to purchase multiple "seats" for a single plan. Apple has introduced two primary channels for this: **Group Purchases** (intended for small teams and social groups via in-app purchase) and **Volume Purchasing** (intended for large-scale enterprises and schools via Apple Business/School Manager). These features allow developers to incentivize bulk purchasing through tiered pricing and manage seat assignments either through Apple’s system or custom API integrations.

---

## Key Concepts and APIs

### 1. Purchasing Channels
*   **Group Purchases:** Triggered directly within your app's UI using StoreKit 2. The purchaser buys a set number of seats and receives an invitation link to share with team members.
*   **Volume Purchasing:** Managed through Apple Business Manager or Apple School Manager. Organizations use their existing device management services to distribute seats at scale.

### 2. Pricing Configuration
*   **Volume Pricing:** Developers can define up to five price bands in App Store Connect. This allows for bulk discounts based on the number of seats purchased (e.g., standard price for 1–20 seats, discounted price for 21–40, etc.).

### 3. Seat Management
*   **Default Management:** If you don't want to build custom infrastructure, Apple provides an out-of-the-box system that handles invitation links, member acceptance, and subscription lifecycle (cancellations/renewals).
*   **Custom Management:** Developers can use new **App Store Server API endpoints** to manage groups, track members, and build custom invitation/onboarding flows within their own application infrastructure.

### 4. Frameworks & Requirements
*   **StoreKit 2:** This is a hard requirement. The feature is available for all auto-renewable subscriptions using StoreKit 2.
*   **App Store Connect:** The central hub for enabling these features, configuring price bands, and setting availability.

---

## Practical Takeaways for Developers

*   **Default Behavior:** 
    *   For most new/existing StoreKit 2 subscriptions, group/organization access is enabled by default.
    *   If your subscription currently uses **Family Sharing**, it is opted out of group/organization selling by default to prevent conflicts. You must manually opt-in via App Store Connect.
*   **Granular Control:** You can restrict a subscription to only be available to educational institutions via Apple School Manager, or opt out of these features entirely if you only wish to sell to individuals.
*   **Merchandising Matters:** When implementing Group Purchases in-app, ensure your UI clearly communicates the value of group collaboration to encourage users to purchase multiple seats rather than just one.
*   **API Utilization:** If your app provides collaborative features (shared cloud storage, project boards, etc.), use the **App Store Server API group management endpoints** to keep your app’s internal group rosters in sync with the App Store's subscription records.

---

## Development Roadmap
1.  **Migrate to StoreKit 2:** If you haven't already, this is the first prerequisite.
2.  **Audit Existing Plans:** Decide which subscriptions are "team-ready" and update your App Store Connect settings accordingly.
3.  **Evaluate Pricing Strategy:** Use the volume pricing bands to calculate if tiered discounts will increase your total recurring revenue.
4.  **Design User Flows:** 
    *   For Group Purchases, create an in-app flow to select seat quantity and initiate the purchase request.
    *   Decide if you will use Apple’s provided invitation/management system or implement a custom flow using the App Store Server APIs.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/391/4/84af4bfe-b42d-4350-91d0-5581899a3e9d/downloads/wwdc2026-391_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/391/4/84af4bfe-b42d-4350-91d0-5581899a3e9d/downloads/wwdc2026-391_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/391/
