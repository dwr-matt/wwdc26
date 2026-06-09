# Find and fix performance issues in your Metal games

這是一份針對 WWDC 2026 Session「Find and fix performance issues in your Metal games」的技術摘要。

---

# 技術摘要：Find and fix performance issues in your Metal games

## 概述
本場次重點介紹了如何針對長期運行的遊戲進行 Metal 效能監控、分析與優化。Apple 在 macOS 與 iOS 27 中引入了更強大的「回溯式（Look-back）」資料收集能力，並推出了全新的 **State Reporting API**，讓開發者能將遊戲邏輯狀態（如關卡、圖形設置）與效能數據連結，從而解決過去在長時段測試中難以定位效能瓶頸的問題。同時，也介紹了如何利用 `MetricKit` 在產品發佈後收集終端用戶的效能指標。

---

## 主要概念與工具

### 1. 效能數據收集與回溯
*   **Metal Performance HUD：** 開發階段的即時效能監控工具，可自訂顯示 FPS、記憶體用量、幀間隔等資訊。
*   **Instruments (Game Performance Overview)：** 用於桌面端分析的標準模板，可結合 CPU 與 Metal 效能指標。
*   **回溯式收集 (Look-back Collection)：** 系統會自動記錄 Metal 效能指標，即使未開啟偵錯工具，也能在遊戲結束後提取過去數小時甚至數日的數據。
    *   **macOS：** 使用命令列工具 `MetalPerfTrace`。
    *   **iOS：** 透過「開發者模式」下的「Performance Trace」功能，在控制中心手動觸發收集。

### 2. State Reporting API (全新)
這是一套全新的 API，允許開發者將「遊戲狀態」注入效能軌跡中。
*   **Domain (領域)：** 代表功能的有限狀態機（例如：Level、Graphics Settings、Network）。
*   **State (狀態)：** 包含 `label`（如 Level1, Level2）與 `stable metadata`（不可變字典）。
*   **Volatile Metadata (易變元數據)：** 用於變動頻率較高的數值（如玩家血量、座標）。

### 3. MetricKit 與後續監控
*   **MetricKit：** 提供「端到端」的分析能力，讓應用程式在背景收集並產出每日報告。在 iOS/macOS 27 中，MetricKit 支援將效能數據依照 `State Reporting` 定義的狀態進行分組聚合。

---

## 程式碼模式：如何導入 State Reporting

開發者需定義一個 `Reporter` 來記錄狀態轉換：

```swift
// 1. 定義並報告狀態 (State Transition)
let graphicsReporter = StateReporter(domain: "com.game.graphics")
graphicsReporter.reportTransition(label: "High", stableMetadata: ["resolution": "4K"])

// 2. 更新易變資料 (Volatile Metadata) - 不觸發狀態轉換
playerReporter.reportVolatileMetadataUpdate(["health": 85, "position": playerPos])
```

---

## 開發者實用重點

### 1. 最佳實踐建議
*   **正交設計 (Orthogonal Design)：** 每個 Domain 應處理單一面向的邏輯（如：不要把「關卡」與「畫質」混在同一個 Domain），避免維護困難。
*   **避免高頻變更：** State Reporting 適用於「玩家操作層級」或更慢的變化。若變更頻率過高（如每幀變更），系統將進行流量管制（Throttling）導致資訊遺失。
*   **先行確認：** 在大規模測試前，先透過 Metal Performance HUD 查看狀態定義是否正確觸發。

### 2. 分析工作流
*   **聚合測試：** 使用 `Metal PerfTrace --json` 輸出格式，可將效能數據匯入腳本或 AI 工具中，進行自動化的回歸測試 (Regression Testing)。
*   **視覺化分析：** 在 Instruments 中，狀態變更會顯示在「Points of Interest」軌跡上。當發現 FPS 下跌時，可直接比對當下遊戲處於哪個 `State`（例如：是否剛好切換到 High Graphics 設定）。

### 3. 發佈後的監控 (Field Data)
*   不要僅關注開發階段。透過 `MetricKit` 收集真實裝置的報告，特別是利用 `State Reporting` 將效能問題關聯至特定場景（例如：在某個特定 Boss 戰場景的平均掉幀率）。
*   善用 `MetricKit` 的 Diagnostics 功能來排查崩潰或記憶體異常原因。

---

**總結：** 透過這套新的工作流，開發者不再需要漫無目的地重現 Bug，而是可以「看見過去」，並透過遊戲狀態的標記，精準定位效能問題發生的脈絡，極大化優化效率。

<!-- resources -->

---

## 資源連結

- [Understanding the Metal Performance HUD metrics](https://developer.apple.com/documentation/Xcode/Understanding-metal-performance-hud-metrics)
- [Monitoring your Metal app’s graphics performance](https://developer.apple.com/documentation/Xcode/Monitoring-your-Metal-apps-graphics-performance)
- [Getting started with StateReporting](https://developer.apple.com/documentation/StateReporting/getting-started-with-statereporting)
- [Metal debugger](https://developer.apple.com/documentation/Xcode/Metal-debugger)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/388/4/682e727f-75f9-441f-81d9-2d6f38bde4b0/downloads/wwdc2026-388_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/388/4/682e727f-75f9-441f-81d9-2d6f38bde4b0/downloads/wwdc2026-388_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/388/
