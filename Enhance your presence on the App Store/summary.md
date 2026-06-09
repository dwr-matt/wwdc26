# Enhance your presence on the App Store

# WWDC 2026: Enhance your presence on the App Store

## Overview
This session introduces new visual storytelling capabilities for the App Store, designed to help developers capture user attention more effectively. Developers can now move beyond standard screenshots and previews by incorporating custom **Product Page Headers** and high-impact **Search Result assets**. By leveraging the new **Asset Library** in App Store Connect, developers can manage, approve, and swap out marketing visuals in real-time without requiring a full app version submission, providing unprecedented flexibility for seasonal campaigns and brand marketing.

---

## Key Concepts

*   **Product Page Header:** A new, customizable space at the top of the App Store product page. It supports images and videos that exist independently of standard screenshots, allowing for brand-focused identity building.
*   **Search Result Assets:** Developers can now replace default screenshot previews in search results with impactful, custom visuals (images or videos) that highlight core app features to drive higher tap-through rates.
*   **Asset Library:** A centralized repository in App Store Connect that houses all marketing media, including screenshots, previews, in-app event media, and the new "Creative Assets."
*   **Creative Assets:** The term used for the new marketing-specific images and videos used in headers and search results.
*   **Real-time Updates:** Once Creative Assets are approved via the Asset Library, they can be deployed to the App Store instantly without submitting a new binary version or going through the review process again.
*   **Integration Ecosystem:** These assets can be synchronized across Custom Product Pages, Apple Ads (Today tab and search ads), and external marketing channels (e.g., website banners) to create a cohesive user journey.

---

## APIs and Frameworks

*   **App Store Connect API:** Developers can automate the upload and submission of Creative Assets to the Asset Library. This allows teams to programmatically manage their store presence as part of a CI/CD pipeline.
*   **Apple Ads Platform API:** Facilitates the automation of ad campaign setups. It includes open-source client libraries (including Swift support) to streamline the process of using Creative Assets in promotional campaigns.

---

## Techniques and Best Practices

*   **Consistent Narratives:** Use the same creative visuals across your website, Custom Product Pages, and Search Result assets. For example, if a user clicks a marketing banner on your website for a specific service (e.g., yoga classes), the App Store landing page should reflect that same visual and deep-link directly into that feature.
*   **A/B Testing:** Utilize **Product Page Optimization** in App Store Connect to test which Creative Assets (logo-focused, feature-focused, or lifestyle-focused) perform best with your target audience.
*   **Pre-approval Strategy:** Upload and get your seasonal assets (e.g., winter vs. summer promotions) approved in the Asset Library well in advance. This allows for instantaneous switching between campaigns without the delay of the app review process.
*   **Previewing:** Always use the new preview functionality in App Store Connect to visualize how assets will appear on iPhone and iPad across different device orientations and locales before submission.

---

## Practical Takeaways for Developers

1.  **Audit your marketing assets:** Start gathering high-quality images and videos that showcase your app’s core value proposition rather than just standard UI captures.
2.  **Centralize via Asset Library:** Migrate your existing media into the new Asset Library to take advantage of the simplified management flow.
3.  **Automate workflows:** For teams with frequent updates or large-scale ad campaigns, integrate the **App Store Connect API** to push assets directly from your internal marketing tools to the store.
4.  **Optimize for conversion:** Use the new header and search result slots to create "aspirational" content—e.g., outdoor apps showing lifestyle imagery or games showcasing cinematic character animations—to stand out in crowded search results.
5.  **Preparation:** Note that these features are available for apps targeting **iOS 27 and iPadOS 27** and later.

<!-- resources -->

---

## Resources

- [Design your own ads with creative assets](https://ads.apple.com/app-store/h/help/design-your-own-ads-with-creative-assets)
- [App Store - What's New](https://developer.apple.com/app-store/whats-new/)
- [Creating your Product Page](https://developer.apple.com/app-store/product-page/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/205/4/47ee16f9-fba0-48a3-9d60-065befef7a95/downloads/wwdc2026-205_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/205/4/47ee16f9-fba0-48a3-9d60-065befef7a95/downloads/wwdc2026-205_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/205/
