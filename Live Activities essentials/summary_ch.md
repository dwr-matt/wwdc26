# Live Activities essentials

這份技術摘要旨在幫助開發者快速掌握 WWDC 2026 Session「Live Activities essentials」的核心內容。本 session 深入探討了如何透過 ActivityKit 與 WidgetKit 構建高效、流暢且能在多個 Apple 平台上自動連動的「即時動態」（Live Activities）。

---

# 技術摘要：Live Activities essentials

## 概述
本 session 介紹了即時動態（Live Activities）的設計精髓與技術實作。即時動態為使用者提供「一眼即知」的即時更新，不僅限於鎖定畫面，更整合至動態島（Dynamic Island）、待機模式（Standby）、Apple Watch Smart Stack 以及 CarPlay。講者透過一個「咖啡訂單」應用程式案例，完整示範了從資料建模、UI 建構到更新策略的實作流程。

## 主要概念與框架
*   **ActivityKit**：管理即時動態的生命週期，負責啟動、更新與結束。
*   **WidgetKit**：用於定義即時動態的視覺呈現，透過 SwiftUI 建構各種尺寸與狀態的介面。
*   **Data Model 架構**：
    *   `ActivityAttributes`：用於存放「靜態資料」（如商店名稱、訂單 ID），生命週期內不可變。
    *   `ContentState`：用於存放「動態資料」（如訂單狀態、剩餘時間），這是更新頻率較高且會變動的部分。
*   **更新策略**：
    *   **應用程式內更新**：適合在 App 前景執行時觸發。
    *   **推播通知 (Push Notifications)**：適合背景更新。支援「廣播模式」（多人同時接收）與「單一通知模式」（針對特定裝置）。

## 關鍵技術實作模式

### 1. 資料模型分離
將靜態與動態資料區分，以達到最佳效能：
```swift
struct DrinkOrderAttributes: ActivityAttributes {
    public struct ContentState: Codable, Hashable {
        var status: String
        var estimatedReadyTime: Date
    }
    // 靜態屬性
    var shopName: String
    var drinkName: String
}
```

### 2. 適應不同尺寸 (Environment Values)
在 iOS 27 中，開發者需處理橫向與縱向的差異。利用環境參數優化佈局：
*   `isDynamicIslandLimitedInWidth`：判斷動態島是否因橫向模式而空間受限。
*   `showsWidgetContainerBackground`：判斷是否應顯示背景，適用於從鎖定畫面轉換至「待機模式」的情境。
*   `activityFamily`：透過判斷裝置家族（如 `.small`），為 Apple Watch 或 CarPlay 提供客製化視圖。

### 3. 互動性 (Interactivity)
透過 **App Intents** 實現直接操作。在 `Live Activity` 中加入按鈕，點擊後觸發指定的 Intent：
```swift
struct RateDrinkIntent: LiveActivityIntent {
    func perform() async throws -> some IntentResult {
        // 處理評分邏輯，並同步至伺服器
        return .result()
    }
}
```

## 開發者實用重點與優化建議

1.  **設計優先**：在實作前，請務必參考《Human Interface Guidelines》與「Design Dynamic Live Activities」session。即時動態的核心價值是「一眼即知（Glanceable）」。
2.  **善用 Stale Date**：在 `ContentState` 中設定 `staleDate`，當資料過期時，系統會自動在 UI 上提示使用者，避免資訊誤導。
3.  **多裝置適配**：
    *   **待機模式 (Standby)**：鎖定畫面設計會被放大至 200%。建議使用 `activityBackgroundTint` 來填滿邊緣，避免留下大量空白。
    *   **動態島**：務必設計 `compactLeading`、`compactTrailing` 與 `minimal` 三種小視圖，並在極小空間（Minimal）保留最關鍵的資訊（例如：倒數圓環）。
4.  **推播通知策略**：
    *   若業務邏輯是針對大規模用戶（如球賽比分），請選擇 **Broadcast (廣播)** 策略。
    *   若為一對一服務（如外送訂單），請使用 **Push Token** 進行針對性更新。
5.  **測試環境**：即時動態在不同裝置（iPhone, Watch, CarPlay）上表現各異，開發時務必針對不同的 `ActivityFamily` 進行 UI 預覽檢查。

---
*欲深入了解 Apple Watch 上的即時動態優化，請進一步參考「Bring Your Live Activity to Apple Watch」session。*

<!-- resources -->

---

## 資源連結

- [Human Interface Guidelines: Live Activities](https://developer.apple.com/design/human-interface-guidelines/live-activities)
- [Starting and updating Live Activities with ActivityKit push notifications](https://developer.apple.com/documentation/ActivityKit/starting-and-updating-live-activities-with-activitykit-push-notifications)
- [ActivityKit](https://developer.apple.com/documentation/ActivityKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/223/4/9098c495-ea8b-44f9-b852-f6eb64840161/downloads/wwdc2026-223_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/223/4/9098c495-ea8b-44f9-b852-f6eb64840161/downloads/wwdc2026-223_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/223/
