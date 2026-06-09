# Explore Retention Messaging in App Store Connect

這份技術摘要旨在幫助開發者快速掌握 WWDC 2026 關於「Retention Messaging（留存訊息）」的重點。這項功能旨在解決訂閱制 App 在使用者嘗試取消訂閱時的關鍵流失問題，透過在取消流程中提供價值主張（Value Proposition），有效提升用戶留存率。

---

# 技術摘要：探索 App Store Connect 中的留存訊息 (Retention Messaging)

## 1. 概述
在 App Store 銷售自動續訂訂閱服務時，如何降低用戶取消訂閱率（Churn Rate）是開發者面臨的巨大挑戰。Apple 推出的 **Retention Messaging** 是一個強大的工具，讓開發者能在用戶決定取消的「最後一刻」與其互動。

本 Session 介紹了兩種主要解決方案：
1. **App Store Connect Retention Messaging**：適合大多數開發者，透過後台配置，由 App Store 自動處理。
2. **Real-Time Retention Messaging**：適合需要高度客製化與動態決策的開發者，透過伺服器對伺服器（Server-to-Server）的 API 即時回應。

---

## 2. 主要概念與機制

### A. Retention Messaging (App Store Connect 基礎版)
* **核心功能**：在訂閱管理頁面中，向用戶顯示「為什麼留下來」的訊息。
* **支援的三種視圖**：
    1. 純文字訊息。
    2. 訊息 + 圖片。
    3. 訊息 + 提供優惠（Retention Offer）。
* **數據成效**：根據測試，採用此功能平均能提升 1.4 個百分點的留存率；若提供促銷優惠，留存率最高可提升 5.5 個百分點（成長達 223%）。
* **設定方式**：在 App Store Connect 的訂閱頁面即可配置，亦可透過 **App Store Connect API** 進行批量處理。

### B. Real-Time Retention Messaging (伺服器即時版)
* **核心功能**：App Store 在用戶觸發取消時，會向開發者的伺服器發送 HTTP 請求，開發者可根據該特定用戶的行為、購買歷史等，動態決定回應內容。
* **額外優勢**：除了基本的訊息與圖片外，還支援 **Switch Plan（切換方案）**，例如提議用戶從月費轉換為年費，或嘗試其他方案而非直接取消。
* **備援機制**：若伺服器回應逾時或錯誤，系統將依序回落至：
    1. App Store Connect 設定的預設留存訊息。
    2. 留存訊息 API 設定的預設訊息。

---

## 3. 技術實作與測試要點

### 測試環境 (Sandbox)
* **完全可測試性**：開發者可於 Sandbox 環境中模擬取消流程，觀察訊息呈現方式。
* **性能測試要求**：使用即時留存訊息 API 必須先通過 Sandbox 的性能測試，確保您的伺服器回應夠快，以維持使用者體驗。

### 數據回傳與 API 互動
* 當用戶兌換優惠時，Signed Transaction 或 Renewal Info 中的 `offerType` 將更新為 `5`（代表 Retention Offer）。
* 若回應「切換方案（Switch Plan）」，需填寫對應的 `productId` 與 `billingPlanType`（例如針對 iOS 26.5 推出的 12 個月承諾期方案）。

### 決策邏輯比較
| 特性 | App Store Connect 版 | Real-Time (API) 版 |
| :--- | :--- | :--- |
| **決策者** | Apple App Store 自動判斷 | 開發者伺服器即時決定 |
| **設定媒介** | ASC 後台 / ASC API | 專用 Retention Messaging API |
| **彈性** | 較低 (預先設定) | 極高 (可根據用戶客製) |
| **備援** | 預設訊息 | 優先讀取 ASC 設定，再讀取 API 設定 |

---

## 4. 開發者的實用重點總結

1. **先從基礎版入手**：即便您打算使用 Real-Time 版，也務必在 App Store Connect 配置基礎的留存訊息，作為系統發生意外時的「備援機制」。
2. **善用「優惠」作為誘因**：數據顯示，提供促銷優惠（Promotional Offers）對提升留存率有顯著效果。請確保您的 App 內已設定好相關的優惠與簽章。
3. **優化伺服器性能**：Real-Time 方案極度依賴 API 回應速度。在進入生產環境前，請務必完成 Sandbox 環境下的性能壓力測試。
4. **申請權限**：Real-Time Retention Messaging 需要申請權限，請透過開發者入口網站的「興趣表單（Interest Form）」進行申請。
5. **關注 Asset Library**：善用 WWDC26 提到的 Asset Library 功能，統一管理留存訊息所需的圖片資源，提升管理效率。

---
*建議進一步閱讀資源：官方 API 文件（連結位於 Session 頁面下方）以及關於「What's New in In-App Purchase」的相關課程。*

<!-- resources -->

---

## 資源連結

- [Interest form: Real-time Retention Messaging](https://developer.apple.com/contact/request/retention-messaging-api/)
- [Supporting monthly subscriptions with a 12-month commitment](https://developer.apple.com/documentation/StoreKit/supporting-monthly-subscriptions-with-a-12-month-commitment)
- [Retention Messaging API](https://developer.apple.com/documentation/RetentionMessaging)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/309/4/afa0aec8-f216-43ed-bcb1-1a3742e49dac/downloads/wwdc2026-309_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/309/4/afa0aec8-f216-43ed-bcb1-1a3742e49dac/downloads/wwdc2026-309_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/309/
