# Bringing Cyberpunk 2077 to Mac

這是一份針對 WWDC 2026 Session「Bringing Cyberpunk 2077 to Mac」的技術摘要，旨在協助開發者深入了解將大型 AAA 遊戲移植至 macOS 與 Apple Silicon 的實踐過程與最佳實踐。

---

# Session 技術摘要：將《Cyberpunk 2077》帶到 Mac

## 1. 概述
本場次由 Apple 遊戲效能團隊的 Garrett Austin 與 CD Projekt Red 的副遊戲總監 Pavel Sosko 共同主講。內容探討了如何將這款擁有龐大開放世界、複雜 AI 與物理互動的 AAA 級遊戲《Cyberpunk 2077》成功移植至 Mac。重點涵蓋了從評估階段（使用 Game Porting Toolkit）、開發階段（Metal 原生轉換），到最終的系統整合（原生功能、顯示器優化與音訊 API 整合）的全流程。

---

## 2. 主要概念、API 與框架介紹

### 評估階段
*   **Game Porting Toolkit (GPTK)：** 在正式投入原生開發前，團隊使用 GPTK 進行 Windows 版本模擬，用於分析 CPU 與 GPU 的效能瓶頸，判斷是否具備移植的可行性。
*   **效能監測工具：**
    *   **MetalHUD：** 用於捕捉追蹤資料（Traces）， correlate 場景操作與渲染行為，特別是在 shader 轉換、存檔與載入時的表現。
    *   **In-engine Profiler：** 進行執行緒（Threads）級別的分析，識別 CPU 的熱點（Hotspots）。

### 渲染與效能優化
*   **Metal API：** 核心渲染框架，負責處理所有的圖形指令。
*   **Metal Shader Converter：** 用於將現有的 shader 轉換為 Metal 兼容格式，並整合進自動化建置管線，達成快速的 Shader 覆蓋。
*   **MetalFX Upscaling：** 透過時序空間升頻（Temporal Upscaling）技術，在維持影像品質的前提下，於高負荷場景釋放 GPU 效能餘裕。
*   **動態解析度縮放 (DRS)：** 配合 MetalFX，根據負載動態調整解析度，確保 FPS 穩定。

### 系統整合與原生體驗
*   **「For This Mac」預設配置：** 一種基於裝置硬體檢測的自動化圖形配置系統，針對每台 Mac 提供最佳的畫質與效能平衡。
*   **NSNotification：** 用於監聽系統事件，實現遊戲與 macOS 的無縫互動（例如：視窗焦點切換、顯示器配置更改）。
*   **Game Mode：** Apple 提供的系統級遊戲模式，優先分配 CPU/GPU 資源，降低背景活動干擾，並提升控制器與音訊裝置（AirPods）的藍牙取樣率以降低延遲。
*   **Extended Dynamic Range (EDR)：** 自動化 HDR 呈現方案，無需使用者手動校準。
*   **Spatial Audio API：** 透過 AV Audio Engine 整合 AirPods 的頭部追蹤（Head-tracking）空間音訊。

---

## 3. 關鍵技術實作模式

### 視窗與系統事件回應
透過 `NSNotificationCenter` 監聽視窗狀態，以節省資源：
*   **暫停渲染：** 監聽 `NSWindow.didChangeOcclusionStateNotification`，根據 `occlusionState` 判斷遊戲是否在背景，進而暫停非必要的渲染。
*   **處理顯示變更：** 監聽 `NSApplication.didChangeScreenParametersNotification` 或 `NSWindow.didChangeScreenNotification`，以即時更新渲染視窗大小與參數。
*   **滑鼠游標管理：** 透過 `windowDidResignKey` 與 `windowDidBecomeKey`，在「遊戲游標」與「系統游標」之間自動切換。

### 自動化 HDR 校準
無需 Calibration 畫面，直接讀取顯示器硬體能力進行 tone mapping：
```swift
// 偽代碼邏輯
let maxEDR = NSScreen.main?.maximumPotentialEDRValue ?? 1.0
if maxEDR > 2.0 {
    // 自動啟用 HDR 並將 maxEDR 值傳遞給 Tone Mapper
    enableHDR(value: maxEDR)
}
```

### 空間音訊頭部追蹤
```swift
// 啟用 AirPods 頭部追蹤
audioEnvironmentNode.listenerAngularOrientation = ...
audioEnvironmentNode.isHeadTrackingEnabled = true
```

---

## 4. 開發者實用重點

1.  **從評估數據出發：** 不要憑空猜測效能瓶頸。利用 GPTK 跑 Hotspot 序列，找出 CPU 壓力（AI/物理）與 GPU 壓力（渲染/Shader 翻譯）的交叉點。
2.  **建立自動化管線：** 將 Metal Shader Converter 整合至 build pipeline 中，讓每次編譯都能自動產出 Metal Shader，而非手動轉換。
3.  **重視「第一印象」：** 開發「For This Mac」預設機制，確保玩家首次啟動遊戲時，體驗就是「針對該機器優化過的」，這是提升 App Store 評價的關鍵。
4.  **擁抱 macOS 原生特性：**
    *   利用 `Game Mode` 降低控制器延遲，這對節奏快的遊戲至關重要。
    *   在遊戲背景化時主動釋放系統資源，能顯著改善使用者對 Mac 系統流暢度的觀感。
5.  **善用 EDR：** 現代 Mac 的顯示器規格優秀，直接透過 API 獲取 EDR 數值來驅動 Tone Mapper，能帶來比傳統手動校準更好的視覺體驗，且減少玩家設定負擔。

本 Session 強調，移植不僅僅是「讓遊戲跑起來」，更是要讓遊戲在 macOS 上「像個原生應用程式（Native App）」一樣運作。

<!-- resources -->

---

## 資源連結

- [Performing your own tone mapping](https://developer.apple.com/documentation/Metal/performing-your-own-tone-mapping)
- [Personalizing spatial audio in your app](https://developer.apple.com/documentation/PHASE/personalizing-spatial-audio-in-your-app)
- [Download the Game Porting Toolkit](https://developer.apple.com/games/game-porting-toolkit/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/356/5/d3ce460b-554d-4760-ae03-072c5acf42aa/downloads/wwdc2026-356_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/356/5/d3ce460b-554d-4760-ae03-072c5acf42aa/downloads/wwdc2026-356_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/356/
