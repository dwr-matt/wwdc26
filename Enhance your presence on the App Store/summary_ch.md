# Enhance your presence on the App Store

這是一份為開發者整理的 WWDC 2026 Session **「Enhance your presence on the App Store」** 技術摘要。

---

# Session 摘要：Enhance your presence on the App Store

## 概述
本 Session 介紹了 App Store 在 iOS 27 與 iPadOS 27 中引入的重大視覺呈現更新。開發者現在可以突破以往僅能使用截圖（Screenshots）和預覽影片的限制，透過「產品頁面標頭（Product Page Header）」與「搜尋結果自訂視覺」來提升 App 的品牌識別度與轉換率。此外，Apple 推出了全新的 **Asset Library** 與更新後的 **App Store Connect API**，讓開發者能更靈活地管理行銷素材，並實現無需重新提交 App 版本即可即時切換頁面內容的動態管理。

---

## 主要概念與功能介紹

### 1. 產品頁面標頭 (Product Page Header)
*   **視覺擴充：** 開發者現在可以在產品頁面最上方添加專屬的 Header 圖片或影片。
*   **品牌塑造：** 不再受限於 App 內截圖，可以使用更具敘事性、品牌識別度的視覺素材。
*   **協同效應：** 透過 Header、App 圖示與下方截圖的組合，建立更完整一致的品牌體驗。

### 2. 搜尋結果呈現 (Search Results Enhancement)
*   **自訂化：** 搜尋結果不再強制顯示預設截圖。開發者可以設定更具衝擊力的圖像或影片，提高使用者點擊意願。
*   **行銷應用：** 這些素材可直接用於 Apple Ads（Today 分頁或搜尋結果廣告），確保廣告內容與產品頁面視覺的一致性。

### 3. Asset Library (資產庫)
*   **集中化管理：** 一站式管理所有 App 相關素材，包含截圖、預覽影片、活動媒體以及新加入的「Creative Assets」（行銷圖像與影片）。
*   **獨立審核流程：** 開發者可以選擇隨 App 版本一同提交審核，或是透過 Asset Library **單獨提交素材審核**。
*   **即時發布：** 一旦素材經審核通過，即可在不更新 App 版本的情況下，直接在 Asset Library 中替換產品頁面或搜尋結果的視覺內容，實現「預先審核、隨時切換」。

---

## 技術實作與 API 應用

### 審核流程的兩大路徑
1.  **版本頁面流程 (Version Submission)：** 適合在發佈新版本時同步更新視覺，透過內建的預覽工具在不同裝置、語言下檢視效果。
2.  **Asset Library 獨立流程：** 適合行銷檔期操作，素材經核准後存入庫中，未來可隨時透過後台或 API 進行替換。

### 自動化工作流
*   **App Store Connect API：** 開發者可利用 API 自動化上傳與提交素材至 Asset Library，減少手動操作。
*   **Apple Ads Platform API：** 支援自動化設定廣告流程，並提供 Swift 等語言的開源客戶端函式庫（Open Source Client Libraries），協助串接與管理廣告視覺。

---

## 開發者實用重點 (Key Takeaways)

*   **跨渠道一致性 (Seamless Experience)：** 建議將行銷網頁、自訂產品頁面 (Custom Product Pages) 與 App 內的深層連結 (Deep Linking) 視覺串聯。例如：廣告點擊後的頁面視覺應與安裝後的首頁體驗呼應。
*   **測試與優化 (Product Page Optimization)：** 利用 App Store Connect 的測試功能，針對不同的受眾測試 Header 圖像或影片效果，數據化決定哪種視覺更能提升轉換率。
*   **提前準備：** 善用「預先審核」機制。在節慶或促銷活動前，先將相關視覺素材提交至 Asset Library，屆時即可在 App Store Connect 中秒級切換，無需等待審核時間。
*   **適配規範：** 務必在開發階段透過 App Store Connect 的預覽工具，確認素材在 iPhone 與 iPad 不同方向、不同語言設定下的呈現效果。

---

**準備下一步：** 建議開發者開始盤點品牌的行銷視覺素材，並嘗試將其導入 Asset Library，以準備迎接 iOS 27/iPadOS 27 帶來的流量轉換新機遇。

<!-- resources -->

---

## 資源連結

- [Design your own ads with creative assets](https://ads.apple.com/app-store/h/help/design-your-own-ads-with-creative-assets)
- [App Store - What's New](https://developer.apple.com/app-store/whats-new/)
- [Creating your Product Page](https://developer.apple.com/app-store/product-page/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/205/4/47ee16f9-fba0-48a3-9d60-065befef7a95/downloads/wwdc2026-205_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/205/4/47ee16f9-fba0-48a3-9d60-065befef7a95/downloads/wwdc2026-205_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/205/
