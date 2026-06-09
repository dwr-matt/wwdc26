# Meet the Music Understanding framework

這是一份關於 **Music Understanding framework** 的技術總結，旨在幫助開發者快速掌握其核心功能與實作方式。

---

# WWDC 2026 技術摘要：Meet the Music Understanding framework

## 1. 概述
Apple 推出的 **Music Understanding framework** 是一個專為音訊分析設計的框架，旨在將專業級的「音樂智能」帶入開發者的應用程式中。此框架能處理所有的訊號處理與模型推論（Model Inference），且全程在**裝置端（On-device）**運行，不僅確保隱私，還能在離線狀態下運作。無論是節拍檢測、節奏同步還是音樂結構分析，開發者無需具備機器學習或音訊訊號處理的專業知識即可使用。

---

## 2. 核心分析維度
該框架提供六大主要分析領域，適用於各種影音編輯、音樂遊戲或 DJ 應用：

*   **節奏（Rhythm）：** 提供 BPM（每分鐘節拍數）、節拍（Beats）與小節（Bars）的時間戳記。
*   **結構（Structure）：** 將歌曲解析為區段（Sections）、片段（Segments）與樂句（Phrases），協助識別副歌、主歌或過門。
*   **音調（Key）：** 識別歌曲的根音（Tonic）與調式（Major/Minor）。
*   **節奏感（Pace）：** 衡量音樂的「感受速度」或能量密度，用於判斷音樂起伏。
*   **樂器活動（Instrument Activity）：** 偵測特定樂器（鼓、貝斯、人聲等）的出現時間段及其強度（0.0 - 1.0）。
*   **響度（Loudness）：** 支援工業標準 LUFS，提供整體響度、瞬間響度（Momentary）、短期響度（Short-term）以及峰值（Peak）。

---

## 3. API 與框架架構

### 核心介面
開發者主要透過 `MusicUnderstandingSession` 進行互動。
1.  **初始化：** 使用 `AVAsset` 或自定義的 `Audio Provider` 初始化 Session。
2.  **執行分析：** 呼叫 `analyze` 方法。可選擇全量分析或針對特定類型的「目標分析（Targeted Analysis）」，後者能有效節省運算效能。
3.  **時間處理：** 框架使用 `CMTime` 與 `CMTimeRange` 作為時間標準，並提供 `TimedValue` 與 `RangedValue` 等結構，方便將音樂事件映射至時間軸。

### 關鍵資料結構
*   **Codable 支援：** 所有的分析結果皆支援 `Codable`，方便開發者將分析數據導出為 JSON 進行持久化儲存或跨裝置共享。
*   **Streaming API：** 對於響度（Loudness）等數據，支援 `AsyncSequence` 串流，適用於需要即時視覺化效果（如響度跳動動畫）的場景。

---

## 4. 實作模式範例

### 初始化與基本分析
```swift
// 使用 AVAsset 初始化
let asset = AVURLAsset(url: fileURL)
// 確保時間精準度
asset.resourceLoader.prefersPreciseDurationAndTiming = true 

let session = MusicUnderstandingSession(asset: asset)
let results = try await session.analyze()
```

### 針對性分析（節省資源）
若只需要響度與結構資訊，可指定類型以避免不必要的計算：
```swift
let results = try await session.analyze(for: [.loudness, .structure])
```

### 響度串流
```swift
let session = MusicUnderstandingSession(asset: asset)
// 設定串流任務
Task {
    for try await loudness in session.loudnessStream {
        updateUI(with: loudness)
    }
}
try await session.analyze()
```

---

## 5. 給開發者的實用重點

*   **效能優化：** 在進行大規模音樂庫處理時，務必使用「目標分析」API，僅請求所需的資料類型。
*   **視覺化應用：**
    *   **影片同步：** 利用「節奏感（Pace）」資訊動態計算片段長度，讓影像切換與音樂節奏完美同步。
    *   **Audio-Reactive 動畫：** 使用 `instrumentActivity` 中的浮點數值（0 到 1）來驅動特效強度。
*   **精確度建議：** 初始化 `AVURLAsset` 時，務必將 `preferPreciseDurationAndTiming` 設為 `true`，這是獲取準確時間戳記的關鍵。
*   **學習資源：** Apple 在開發者網站提供了 **"Music Understanding Lab"** 的範例專案，強烈建議下載閱讀其程式碼，特別是關於如何將分析結果映射到 UI 元件（Tiles）的部分。

### 結語
Music Understanding framework 將 Apple 在 Final Cut Pro 等專業軟體中使用的技術下放給開發者。這套工具極大地降低了多媒體應用程式開發的門檻，讓您能專注於打造更具沉浸感的音樂體驗，而非底層的演算法實作。

<!-- resources -->

---

## 資源連結

- [Creating visuals with Music Understanding analysis results](https://developer.apple.com/documentation/MusicUnderstanding/create-visuals-using-musicunderstanding-analysis-results)
- [MusicUnderstanding](https://developer.apple.com/documentation/MusicUnderstanding)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/253/5/db1c3715-aaaf-42db-8e9e-66d2a0011430/downloads/wwdc2026-253_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/253/5/db1c3715-aaaf-42db-8e9e-66d2a0011430/downloads/wwdc2026-253_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/253/
