# Meet the new MetricKit

這是一份關於 WWDC 2026 Session 「Meet the new MetricKit」的詳細技術摘要。

---

# WWDC 2026 技術摘要：Meet the new MetricKit

## 概述
本 session 介紹了 iOS 27 中徹底重構的 **MetricKit** 框架。Apple 將 MetricKit 定位為應用程式性能監控的核心工具，旨在協助開發者收集生產環境下的真實數據，並透過「指標 (Metrics)」監控健康狀況、「診斷 (Diagnostics)」定位錯誤根源。本次更新重點在於引入了現代化、以 Swift 為核心的 API，以及全新的「狀態感知 (State-aware)」報告功能，讓開發者能更精準地將效能問題與特定的應用程式狀態（如：不同的 UI 分頁或實驗性功能）進行關聯分析。

---

## 主要概念與架構

### 1. 兩大核心數據流
*   **Metrics（性能指標）：** 提供應用程式整體的健康信號（如：啟動時間、掛起時間、記憶體/CPU 使用量、全新的 **Metal Frame Rate**）。用於識別效能趨勢。
*   **Diagnostics（診斷報告）：** 當發生異常（如崩潰、記憶體異常終止）時，提供詳細資訊（如回溯追蹤 backtraces）。用於找出程式碼層級的錯誤根源。

### 2. 新一代 API
iOS 27 移除了舊有的 `MXMetricManager` 模式，採用全新的 `MetricManager`。新 API 的特點：
*   **Swift-First：** 語法更簡潔、類型更安全。
*   **結構化數據：** 報告皆符合 `Codable` 協定，極易轉換為 JSON 發送到後端伺服器進行聚合分析。

### 3. 狀態感知報告 (State-aware Reporting)
這是本次更新的最大亮點。開發者可以透過 `State Reporting` 框架定義應用程式的「領域 (Domain)」與「狀態 (State)」。
*   **運作機制：** 在不同頁面或功能切換時回報狀態，MetricKit 會自動將該時段的效能數據與該狀態進行「交叉分析 (Intersection)」。
*   **優勢：** 不再只看到整體的平均性能數據，而是能區分「報告分頁」與「消費分頁」效能表現的差異，從而精準抓出導致效能瓶頸的特定程式碼路徑。

---

## 程式碼模式與實作

### 初始化與接收報告
開發者需在 `AppStartup` 時期初始化 `MetricManager`，確保不會遺漏數據：

```swift
// 設定並監聽 Metric/Diagnostic 報告
let manager = MetricManager.shared
Task {
    for await report in manager.metricReports {
        // 將報告轉為 JSON 發送到後端
        let data = try JSONEncoder().encode(report)
        uploadToServer(data)
    }
}
```

### 處理結構化指標
若需針對特定指標（如 Peak Memory）進行分析，可直接遍歷報告結構：

```swift
for entry in report.intervalEntries {
    for group in entry.metricGroups {
        if let memoryGroup = group as? MemoryMetricGroup {
            let peakMemory = memoryGroup.peakMemory
            print("Peak memory: \(peakMemory)")
        }
    }
}
```

### 回報應用程式狀態 (State Reporting)
透過定義 Domain 與 State 來增加數據維度：

```swift
// 定義並轉換狀態
let reporter = StateReporter(domain: "com.myapp.tabs")
reporter.report(state: "reports_tab")

// 進階：加入自定義 Metadata (需標註 @ReportableMetadata)
struct ViewConfig: ReportableMetadata {
    let listSize: String
    let isSorted: Bool
}
reporter.report(state: "reports_tab", metadata: ViewConfig(listSize: "large", isSorted: true))
```

---

## 開發者實用重點

1.  **遷移策略：** 若專案仍在依賴舊版的 `MXMetricManager`，應儘速規劃遷移至 iOS 27 的新 `MetricManager` API，以獲取最新的診斷能力（如 Memory Exception Diagnostics）。
2.  **避免過度細分狀態：** 雖然狀態感知功能強大，但過多的狀態定義會導致數據過於碎片化，反而增加分析難度。應專注於定義「穩定且具意義」的應用程式相位。
3.  **後端聚合分析：** MetricKit 僅負責收集與傳遞，數據科學價值源於後端的聚合。建議設定統計監控，當特定指標（如掛起時間增加）出現趨勢性偏移時觸發警報。
4.  **驗證工具：** 在發布前，務必使用 Xcode 的 **Points of Interest (POI)** 工具來驗證你的狀態轉換邏輯是否符合預期，確保數據記錄與真實的使用情境吻合。
5.  **診斷優先：** 當發生崩潰或異常終止時，優先檢查 `terminationCategory` 與 `backtrace`，這能直接縮小除錯範圍，大幅節省 Triage 時間。

---
*註：本摘要根據影片內容整理，涉及技術細節請參閱官方 API 文件。*

<!-- resources -->

---

## 資源連結

- [Getting started with StateReporting](https://developer.apple.com/documentation/StateReporting/getting-started-with-statereporting)
- [Analyzing app performance with MetricKit](https://developer.apple.com/documentation/MetricKit/analyzing-app-performance-with-metrickit)
- [Monitoring app performance with MetricKit](https://developer.apple.com/documentation/MetricKit/monitoring-app-performance-with-metrickit)
- [Track performance by app state using MetricKit](https://developer.apple.com/documentation/MetricKit/track-performance-by-app-state-using-metrickit)
- [MetricKit](https://developer.apple.com/documentation/MetricKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/222/4/86b76599-f095-4bd8-8004-f1dbd1bacb84/downloads/wwdc2026-222_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/222/4/86b76599-f095-4bd8-8004-f1dbd1bacb84/downloads/wwdc2026-222_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/222/
