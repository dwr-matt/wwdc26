# Validate your App Intents adoption with AppIntentsTesting

這是一份針對 WWDC 2026 Session **「Validate your App Intents adoption with AppIntentsTesting」** 的技術摘要。

---

# 技術摘要：使用 AppIntentsTesting 驗證 App Intents 的採用

## 概述
隨著 App Intents 在 Apple 生態系中的重要性日益增加（涵蓋 Siri、捷徑、Spotlight 搜尋與 Widget），確保其穩定性至關重要。本 Session 介紹了全新的 **AppIntentsTesting** 框架，這是一套專為測試 App Intents 堆疊所設計的整合測試框架。開發者現在可以透過與應用程式分離的測試程序，對 Intent、Entity、Query 及系統級整合（如 Spotlight 與 View Annotations）進行自動化測試，無需依賴 UI 自動化測試的脆弱性，也無需在測試目標中匯入應用程式原始碼。

---

## 主要概念與框架介紹

### 1. 測試架構
*   **跨行程架構 (Out-of-process)：** 測試執行器（Test Runner）與 App 在不同的處理序中執行。這意味著測試程式不會分享狀態，且不需要將應用程式程式碼編譯進測試目標中。
*   **非 UI 依賴：** 測試透過定義檔而非 UI 元素查找，這使得測試更加穩定，即便介面重構也不易失效。
*   **全堆疊驗證：** 測試會完整走過 App Intents 的執行路徑，確保與真實使用者體驗一致，無需 Mock 或 Stub。

### 2. 核心組件
*   **Intent Definitions：** 透過 `Bundle Identifier` 來存取應用程式內的 Intent、Entity 與 Query 定義，無需匯入應用程式模組。
*   **Test-only Intents：** 開發者可以建立專門用於測試的 Intent，並透過 `isDiscoverable = false` 與 `#if DEBUG` 標籤隔離，用於執行資料重置、狀態操控或模擬導覽。
*   **動態成員查找 (Dynamic Member Lookup)：** 在測試時，可以方便地存取 Entity 的屬性來進行斷言（Assertion）。

---

## 示範的程式碼模式與技術

### 1. 撰寫基礎測試
使用 `Intents` 屬性透過訂閱（Subscript）存取定義，並呼叫 `makeIntent`：
```swift
// 建立一個 Intent 定義並執行
let intentDef = IntentDefinitions(bundleIdentifier: "com.example.CometCal").intents["CreateCalendarIntent"]
let intent = intentDef.makeIntent(parameters: ["name": "OccupySaturn", "color": "red"])

// 執行並獲取結果
let result = try await intent.run()
let newCalendar = result.value
XCTAssertEqual(newCalendar.title, "OccupySaturn")
```

### 2. 測試-驅動開發 (TDD) 與 Query 驗證
透過 `entity.matching` 測試 Entity 的 String Query 搜尋功能：
```swift
// 執行查詢並斷言
let results = try await EventEntity.matching(query: "Cosmic Ray")
XCTAssertEqual(results.count, 1)
XCTAssertEqual(results.first?.title, "Cosmic Ray Rebuilding")
```

### 3. 測試系統整合
*   **Spotlight：** 使用 `Spotlight.query` 驗證資料是否正確索引至系統搜尋。
*   **View Annotations：** 使用 `EventEntity.viewAnnotations` 驗證畫面是否正確向系統宣告當前呈現的 Entity，這對於 Siri 語境感知至關重要。

---

## 開發者的實用重點

1.  **設定要求：** 測試目標（Test Bundle）與應用程式必須使用相同的開發團隊（Development Team）進行簽署。
2.  **型別處理：** 
    *   大多數基本型別可自動轉換。
    *   自定義型別需參考 `IntentValueConvertibleWrapper` 文件進行封裝。
3.  **最佳實踐：**
    *   **自包含測試：** 每個測試都應是獨立的。利用 `Test-only Intents` 在 `setUp` 方法中注入必要的測試資料，避免狀態污染導致的 Flaky Tests。
    *   **整合測試層次：** 先驗證基礎的 Action 與 Query，再進階測試系統級整合（Spotlight、View Annotations）。
    *   **除錯與驗證：** 除了自動化測試，仍建議定期手動透過 Siri 與捷徑 App 進行最終的真實環境驗證。

### 結論
`AppIntentsTesting` 將原本困難的 App Intents 除錯轉變為標準的軟體測試流程。透過這套框架，開發者可以確保核心業務邏輯在多個 iOS 版本間保持穩定，並無縫連接 Apple 的系統級服務。開發者應下載 **Comet Cal** 範例專案，深入研究其中的測試實作範例。

<!-- resources -->

---

## 資源連結

- [Testing your App Intents code](https://developer.apple.com/documentation/AppIntentsTesting/testing-your-app-intents-code)
- [App Intents Testing](https://developer.apple.com/documentation/AppIntentsTesting)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/295/4/cdcee6d3-e3e9-4201-b1ef-cd33e2d10e6f/downloads/wwdc2026-295_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/295/4/cdcee6d3-e3e9-4201-b1ef-cd33e2d10e6f/downloads/wwdc2026-295_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/295/
