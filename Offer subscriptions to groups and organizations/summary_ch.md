# Offer subscriptions to groups and organizations

這份技術摘要整理了 WWDC 2026 關於「提供團體與組織訂閱」的核心內容，幫助開發者快速導入此功能。

---

# WWDC 2026 技術摘要：為群組與組織提供訂閱服務 (Offer subscriptions to groups and organizations)

## 概述
本議程介紹了如何擴展 App Store 訂閱模式，使其能夠支援「群組」與「組織」等級的購買。透過此機制，開發者可以將訂閱服務銷售給企業、學校或小型社交團體，並透過兩種主要的購買路徑來擴大應用程式的使用規模。

## 主要概念與功能
蘋果提供了兩種全新的訂閱購買途徑，這些功能均需基於 **StoreKit 2** 實作：

1.  **大量採購 (Volume Purchasing)：**
    *   透過 **Apple Business Manager** 與 **Apple School Manager** 進行。
    *   適用於大型組織，可結合 MDM（行動裝置管理）系統大規模派發與管理授權席次 (Seats)。
2.  **群組購買 (Group Purchases)：**
    *   直接在 App 內發起購買。
    *   適合小型團隊或社交群組。購買者購買多個席次後，透過分享「邀請連結」來邀請成員加入。

## 技術實作與架構

### 1. 訂閱配置 (App Store Connect)
*   **預設行為：** 所有使用 StoreKit 2 的新訂閱預設啟用此功能。若該訂閱已啟用「家庭共享 (Family Sharing)」，則預設關閉，需開發者手動開啟。
*   **定價策略 (Volume Pricing)：** 開發者可設定多達 5 個價格區間 (Price Bands)，針對購買較多席次的客戶提供階梯式折扣，鼓勵大宗採購。

### 2. 購買流程與席次管理
*   **購買流程：** 開發者需在 App 內設計介面，向客戶確認購買的席次數量，並將此數量傳入 StoreKit 2 的購買請求 (Purchase Request)。
*   **系統管理 (內建)：** 若不想自行開發管理系統，蘋果提供了一套內建機制，負責：
    *   產生邀請連結。
    *   追蹤成員加入與授權指派。
    *   處理訂閱生命週期（如取消訂閱）。
*   **自訂管理 (App Store Server API)：** 若開發者已有現成的成員管理系統，可透過全新的 **App Store Server API** 終端點來存取群組資訊、成員列表，實現與自建系統的整合。

## 關鍵技術重點

### 流程建議
*   **Merchandising (銷售展示)：** 在 App 內 UI 應適當強調「群組購買」的優勢（如：團隊協作、價格折扣），以提升轉化率。
*   **席次轉移：** 一旦邀請被接受，App Store 會為每個成員分配一個交易 (Transaction)，開發者即可授予該成員對應的存取權限。

### 開發者實用清單 (Action Items)
1.  **升級至 StoreKit 2：** 這是實作此功能的先決條件。
2.  **檢視現有訂閱：** 檢查 App Store Connect 中的訂閱設定，確認是否需要調整或開啟團體銷售。
3.  **規劃定價模型：** 評估是否採用「大量採購折扣 (Volume Pricing)」，計算各階梯價格對營收的影響。
4.  **體驗整合：** 思考如何讓 App 內的協作功能與訂閱席次無縫銜接。如果你希望強化使用者體驗，請務必研究新增的 App Store Server API 群組管理介面。

## 總結
此更新讓開發者能更輕易地進入 B2B 與社群市場。透過靈活的「大量採購」與「群組購買」機制，配合 StoreKit 2 的原生支援，開發者可以更精準地針對不同規模的組織設定價格，並透過簡單的 API 整合，擴大應用程式的市場影響力。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/391/4/84af4bfe-b42d-4350-91d0-5581899a3e9d/downloads/wwdc2026-391_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/391/4/84af4bfe-b42d-4350-91d0-5581899a3e9d/downloads/wwdc2026-391_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/391/
