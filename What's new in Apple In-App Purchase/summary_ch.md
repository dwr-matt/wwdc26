# What's new in Apple In-App Purchase

這是一份針對 WWDC 2026 Session「What's new in Apple In-App Purchase」的技術摘要。

---

# WWDC 2026 技術摘要：Apple In-App Purchase 新功能概覽

## 概述
本場 Session 介紹了 StoreKit 與 App Store Connect 在 2026 年的重大更新，旨在協助開發者優化訂閱模式、提升轉化率並簡化審核流程。主要涵蓋三大領域：**帶有 12 個月承諾的月費訂閱制**、**Offer Code Redemption API 更新**，以及 **App Store Connect 增強版審核提交體驗**。

---

## 主要概念與 API 介紹

### 1. 訂閱定價新機制：月繳制的 12 個月承諾 (Monthly with 12-month commitment)
iOS 26.5 引入了一種新的訂閱形式，允許開發者將「年度訂閱」以「月繳」方式提供給用戶。
*   **靈活性**：用戶可選擇一次付清或分 12 期繳納，降低了長期訂閱的門檻，有助於擴大用戶群。
*   **管理**：在 App Store Connect 中，可將此定價選項加入既有的年度自動續訂項目中。
*   **平台支援**：適用於 iOS、iPadOS、macOS、tvOS 與 visionOS 26.4 以上版本。

### 2. 訂閱組合 (Bundles & Suites)
*   **Bundle**：多個訂閱項目的組合，用戶可單獨購買，但組合購買享優惠。
*   **Suite**：僅在「組合內」存在的訂閱，無法單獨購買，通常用於一組相關聯的 App 服務。
*   *備註：此功能預計於 2026 年稍晚釋出更多細節，開發者可在 Xcode 27 進行初步測試。*

### 3. Offer Code Redemption API 更新
增強了代碼兌換的 API，提供更即時的反饋：
*   **Verification Result**：兌換完成後，API 現在會直接回傳驗證結果（Transaction 物件或錯誤資訊）。
*   **跨平台支援**：提供 SwiftUI 與 UIKit (`Present OfferCode Redeem Sheet`) 的對應版本。

---

## 程式碼模式與實作技術

### 1. Merchandising（商品呈現）
透過 `SubscriptionInfo` 中的 `PricingTerms` 陣列來判斷是否顯示月繳承諾選項。

```swift
// 在 SwiftUI 中使用 StoreKit View 篩選定價條款
SubscriptionStoreView(groupID: "your_group_id")
    .preferredSubscriptionPricingTerms { pricingTerms in
        // 過濾出月繳方案
        pricingTerms.filter { $0.billingPlanType == .monthly }
    }
```

### 2. 取得交易資訊與承諾進度
透過 `Transaction` 新屬性檢查用戶的承諾狀態：
*   若 `commitmentInfo` 為 `nil`，表示為一次性付清。
*   若為 `monthly`，可讀取 `progress`、`price` 與 `expiration` 等欄位，用來建構 UI 中的進度條。

### 3. 處理續訂資訊 (Renewal Info)
使用 `RenewalInfo` 來獲取用戶在「12 個月承諾期滿後」的續訂偏好設定。這對於伺服器端監控訂閱生命週期至關重要。

---

## 開發者實用重點

### 測試環境 (Xcode 27)
*   **StoreKit Config**：在 Xcode 27 的設定檔中，可直接為一年期訂閱選擇「Monthly with 12-month commitment」進行測試。
*   **Transaction Manager**：利用此工具測試購買流程，並在 Inspector 中驗證 `commitmentInfo` 的數值是否正確。

### 審核提交流程的改變
*   **統一審核 (Unified Submission)**：App Store Connect 現在允許將 In-App Purchase 產品與「App 內活動」、「自訂產品頁面」等項目合併為單一審核請求。
*   **自動化遷移**：App Store Connect API 已更新，建議開發者從既有的 IAP 資源遷移至 `Review Submissions` 與 `Review Submission Items` API，以實現自動化管理。

### 建議下一步
1.  **評估定價策略**：考慮現有的年度訂閱是否適合加入「12 個月月繳承諾」，以提升轉換率。
2.  **更新 API 呼叫**：檢查並更新全域的 Offer Code 兌換代碼，以處理新的驗證結果物件。
3.  **遷移 API**：著手將審核提交流程遷移至新的 `Review Submissions` API 集合。
4.  **監控 retention**：參考「Explore Retention Messaging in App Store Connect」相關 Session，利用伺服器端 API 執行用戶留存策略。

---
*技術編輯提示：上述功能需配合 iOS 26.4+ 與 Xcode 27 環境，請儘早於 Sandbox 進行驗證，特別是針對新的訂閱生命週期處理邏輯。*

<!-- resources -->

---

## 資源連結

- [In-App Purchase types](https://developer.apple.com/help/app-store-connect/reference/in-app-purchases-and-subscriptions/in-app-purchase-types)
- [Managing the life cycle of monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/managing-lifecycle-of-monthly-subscriptions-with-a-12-month-commitment-)
- [Supporting monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/supporting-monthly-subscriptions-with-a-12-month-commitment)
- [App Store Server Notifications V2](https://developer.apple.com/documentation/AppStoreServerNotifications/App-Store-Server-Notifications-V2)
- [Supporting offer codes in your app](https://developer.apple.com/documentation/StoreKit/supporting-offer-codes-in-your-app)
- [Implementing a store in your app using the StoreKit API](https://developer.apple.com/documentation/StoreKit/implementing-a-store-in-your-app-using-the-storekit-api)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/210/4/f029ab19-6670-48c6-b9b1-88ac6692cdda/downloads/wwdc2026-210_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/210/4/f029ab19-6670-48c6-b9b1-88ac6692cdda/downloads/wwdc2026-210_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/210/
