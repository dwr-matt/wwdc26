# Platforms State of the Union

這是一份關於 **WWDC 2026: Platforms State of the Union** 的詳細技術摘要，旨在幫助開發者快速掌握今年 Apple 生態系的重大更新與技術方向。

---

# 技術摘要：2026 Platforms State of the Union

## 概述
本屆 State of the Union 聚焦於三個核心戰略領域：**Apple Intelligence (AI)**、**平台架構優化**，以及**開發者生產力提升**。Apple 透過強化原生框架與 AI 整合，賦予開發者將「生成式 AI」深度嵌入應用程式的能力，同時透過 Xcode 27 的「代理式編碼 (Agentic Coding)」進化，重新定義開發工作流程。

---

## 主要概念、API 與框架介紹

### 1. Apple Intelligence 與模型框架
*   **Foundation Models Framework**: 提供統一的 Swift API，支援在設備端（On-device）運行模型，並能無縫切換至 Private Cloud Compute 或第三方伺服器模型（如 Claude, Gemini）。
    *   **新特性**: 支援多模態提示詞（文字與影像）、整合 Vision Framework（OCR、條碼辨識）。
    *   **免費額度**: 開發者若 App 首次安裝量少於 200 萬，可免費使用 Private Cloud Compute 資源。
*   **Core AI**: 專為高效能 On-device 運算設計的底層框架，支援記憶體安全（Memory-safe）的 Swift API、自訂 GPU Kernel 以及 Python 轉換工具，適用於需要極致效能的複雜模型。
*   **App Intents Framework**: 連接 App 與系統層級智能的核心。透過 **Intents Schemas** 與 **Entity Schemas**，讓 Siri 能理解 App 內容，實現語音控制、Spotlight 語義搜尋與跨 App 的場景連動。

### 2. 平台優化與 SwiftUI 升級
*   **Liquid Glass 設計語言更新**: 強化了深度感與遮光效果，提供更彈性的透明度與色彩調整。macOS 現在支援 `Show Borders`，且邊角半徑（Corner Radius）全系統統一。
*   **SwiftUI 新增功能**:
    *   **互動性**: 新增 `reorderable` (拖曳排序) 與 `swipe actions` 容器。
    *   **適應性**: 新增 `visibility priority` 修飾符，用於自定義 Toolbar 內容的優先級。
    *   **文件架構**: 新的 Document API 提供一級 URL 訪問權限，實現高效的部分讀寫與觀測。
    *   **Spatial Preview**: 將 Mac App 的 3D 物件串流至 Apple Vision Pro 進行即時預覽與互動。

### 3. Swift 語言與工具鏈
*   **Swift 6.4**: 強調編譯效能與開發者體驗，優化了複雜表達式的型別檢查（Type-checking）錯誤訊息，並允許在 `defer` 區塊中使用 `async` 呼叫。
*   **核心 OS 重構**: Apple 已將部分作業系統核心組件改寫為 Swift，證明了 Swift 在系統底層開發的效能與安全性。

---

## 程式碼模式與技術示範

### Dynamic Profiles (Foundation Models Framework)
透過 Swift Result Builder 語法，開發者可以即時切換不同的 AI 模型配置，無需銷毀 Session：

```swift
// 動態調整 AI 模型行為
let profile = LanguageModelProfile {
    // 根據 App 狀態動態更換 AI 能力與工具
    if tutorialGenerated {
        SystemInstruction("提供高品質的回饋")
        Tool("OrigamiFeedbackTool")
    } else {
        SystemInstruction("根據圖片產生創意專案")
        Model("PrivateCloudCompute", temperature: 0.8)
    }
}
```

### View Annotations (App Intents)
透過新的 View Modifier，將 UI 元素與語義實體繫結，讓 Siri 能直接操作畫面上的內容：
```swift
MessageListView()
    .annotate(messageEntity) // 告知 Siri 該 UI 代表哪個實體
```

---

## 開發者實用重點

1.  **AI 的分層架構策略**:
    *   簡單任務使用 **Foundation Models Framework**（On-device/Private Cloud）。
    *   需要自訂模型或極致效能時使用 **Core AI**。
    *   研究與細調模型使用 **MLX**。
2.  **Xcode 27 的 AI 革命**:
    *   **代理式編碼 (Agentic Coding)**: 透過 `/plan` 命令，AI 可自動分析專案結構、製作圖表並生成程式碼。
    *   **Device Hub**: 取代傳統 Simulator，整合真實裝置管理，支援跨螢幕尺寸測試與真實硬體互動。
    *   **Settings Sync**: 開發者設定（含 Git 配置）現在可透過 iCloud 自動同步至新設備。
3.  **App 可適性 (Adaptability)**:
    *   Apple 推動 iOS App 在 macOS (iPhone Mirroring) 與 iPad 上的彈性調整。只要使用 SwiftUI 或 Auto Layout，App 即可自動支援視窗大小調整。
4.  **從舊設計遷移**: 隨著 Xcode 27 釋出，Apple 將移除對舊設計（Legacy Design）的支援，重新編譯後 App 將強制套用新的 Liquid Glass 風格，開發者應儘早檢查 UI 佈局。

### 資源連結提醒
所有技術細節與超過 100 場的深度技術 Session，皆可透過 Apple Developer App、官網、YouTube 以及新增的 **Bilibili** 頻道觀看。建議針對「Apple Intelligence」、「SwiftUI 互動性」與「Xcode 代理編碼」進行深入研究。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/102/2/abb4bd38-dfae-46cf-985f-160769b92d41/downloads/wwdc2026-102_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/102/2/abb4bd38-dfae-46cf-985f-160769b92d41/downloads/wwdc2026-102_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/102/
