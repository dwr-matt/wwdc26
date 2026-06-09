# Unlock in-game content with StoreKit and Background Assets

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 Session「Unlock in-game content with StoreKit and Background Assets」的核心重點。

---

# 技術摘要：利用 StoreKit 與 Background Assets 解鎖遊戲內容

## 概述
本 session 介紹了 Apple 在遊戲開發領域的兩大進階更新：**Managed Background Assets（背景資產管理）** 的在地化增強，以及專為 Unity 開發者推出的 **StoreKit 與 Background Assets 原生外掛（Plugins）**。這些工具旨在協助開發者優化遊戲載入體驗、降低應用程式初始下載體積，並透過更完善的 API 整合，簡化跨平台的遊戲商業化流程。

---

## 主要概念與框架更新

### 1. Managed Background Assets 的在地化支援 (iOS 27+)
Managed Background Assets 允許系統根據玩家的語言偏好，自動下載並安裝對應的資產包（Asset Packs），而非預載所有語言資源。
*   **語言回退機制（Fallback Mechanism）：** 
    *   若玩家偏好語言（如德文）有對應資產包，則僅安裝該包。
    *   若無完全匹配，系統會嘗試匹配區域變體（如 English-UK 回退至 English-US）。
    *   若兩者皆無，則回退至遊戲設定的預設語言（Primary Language）。
*   **Steam 資源轉換工具：** 透過 `xcrun ba.package.convert` 指令，開發者可將現有的 Steam Depots 直接轉換為 Apple 的 Asset Pack 格式，簡化移植流程。

### 2. Apple Unity Plugins 新增項目
Apple 官方釋出了兩款新的 Unity 外掛，作為 C# API 與 Apple 原生框架之間的橋樑：
*   **StoreKit Plugin：** 提供與 Apple 原生 StoreKit 對等的 API，用於產品展示、購買流程與交易驗證。
*   **Background Assets Plugin：** 允許在 Unity 中管理資產包的下載進度、狀態更新與本地檢查。

---

## 技術實作與程式碼模式

### Unity 專案中的交易處理流程
開發者應使用 `Transaction Update` 監聽器處理跨裝置購買或外部購買請求：

```csharp
// 交易更新監聽與驗證邏輯
void OnTransactionUpdate(Transaction transaction) {
    if (transaction.isRevoked) {
        // 處理撤銷狀態
    } else if (transaction.isVerified) {
        // 確認交易後，核發遊戲內容
        GrantContentToPlayer(transaction);
        // 完成交易
        transaction.Finish();
    }
}
```

### 監控背景下載進度
在 Unity 中整合 Background Assets 時，可透過非同步方法監聽下載狀態，並即時更新 UI：

```csharp
// 監控資產包下載進度
async Task MonitorDownload(AssetPack pack) {
    await foreach (var status in pack.DownloadStatusUpdates) {
        UpdateUIProgressBar(status.Progress);
        if (status.IsCompleted) {
            LoadGameContent();
        }
    }
}
```

### Xcode 測試環境設定
*   **StoreKit Testing：** 在 Xcode 27 中配置 `.storekit` 設定檔。
*   **Mock Server：** 於 Xcode Target Scheme 的 `Run` 設定中，指定存放 Asset Pack 的資料夾，Xcode 27 會自動啟動背景模擬伺服器（Mock Server）來測試 asset 載入邏輯。

---

## 開發者實用重點總結

1.  **降低安裝體積：** 務必在 App Store Connect 上傳資產包的「在地化版本」，這能顯著提升安裝率，並節省玩家儲存空間。
2.  **Unity 開發標準化：** 使用官方的 `Background Assets` 與 `StoreKit` 外掛，這些外掛已針對 Apple 平台進行效能優化，且支援完整的 C# 介面，無需手動編寫大量原生 Objective-C/Swift 橋接代碼。
3.  **交易確認的核心原則：**
    *   針對消耗型（Consumable）項目：驗證 transaction 是否被撤銷。
    *   針對非消耗型（Non-consumable）與訂閱項目：始終以 `Current Entitlements` 為唯一真實來源（Source of Truth）。
4.  **視覺行銷：** iOS 27 引入了全新的支付介面（System Payment Sheet），支援橫向模式（Landscape Mode），提升遊戲內的購買體驗。同時，善用新的 App Store 產品頁面標題圖示與搜尋結果預覽，將有助於提升遊戲在 App Store 及 Apple Games App 中的曝光度。

**資源連結建議：**
*   請至 GitHub 下載最新的 Apple Unity Plugins。
*   觀看往年相關 Session 如《Discover Apple Hosted Background Assets》(WWDC25) 與《Plugin and Play: Add Apple Frameworks to Your Unity Game Projects》(WWDC22) 以補強基礎架構知識。

<!-- resources -->

---

## 資源連結

- [Apple Unity Plug-Ins on GitHub](https://github.com/apple/unityplugins)
- [Background Assets](https://developer.apple.com/documentation/BackgroundAssets)
- [StoreKit](https://developer.apple.com/documentation/StoreKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/378/6/16c93f95-21e8-4f7f-bb96-2b3c682fa6c7/downloads/wwdc2026-378_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/378/6/16c93f95-21e8-4f7f-bb96-2b3c682fa6c7/downloads/wwdc2026-378_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/378/
