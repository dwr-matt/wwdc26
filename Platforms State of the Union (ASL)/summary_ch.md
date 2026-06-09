# Platforms State of the Union (ASL)

這是一份為開發者整理的 **WWDC 2026: Platforms State of the Union** 技術摘要。本屆大會的核心主軸為「AI 深度整合」與「開發者生產力提升」，Apple 透過一系列框架更新，讓開發者能更無縫地將 Apple Intelligence 引入應用程式中。

---

# WWDC 2026: Platforms State of the Union 技術摘要

## 1. 概述
本場演講重點聚焦於如何利用 Apple 的平台基礎建設，構建具備高度智能與適應性的應用程式。主要涵蓋三大範疇：
*   **Apple Intelligence 的應用與整合**：從 Foundation Models 到 App Intents。
*   **平台現代化改進**：包含 Liquid Glass 設計語言的精進、SwiftUI 的效能優化與架構升級。
*   **開發者生產力**：Xcode 27 的智能代理（Agentic coding）、全新的 Device Hub 與 Agent Client Protocol。

---

## 2. 主要概念、API 與框架介紹

### A. Apple Intelligence 相關框架
*   **Foundation Models Framework**：
    *   提供原生 Swift API，支援多模態提示（Text + Image）。
    *   新增 **Server Models** 支援（如 Claude, Gemini），並允許開發者透過 Swift Package 擴充模型提供者。
    *   **Dynamic Profiles**：一套宣告式 API，允許在模型執行過程中動態切換模型配置、提示詞或工具集，無需重新初始化 session。
*   **Core AI**：
    *   一個全新的高效能框架，專為在 Apple Silicon 上運行客製化 PyTorch 模型而設計，提供記憶體安全、硬體加速及視覺化 Tensor 調試。
*   **App Intents Framework**：
    *   透過 **Schemas**（Entity 與 Intent）讓系統深入理解 App 功能。
    *   **View Annotations API**：連結 UI 元素與 App Entity，讓使用者能透過自然語言針對畫面上的內容進行操作。

### B. 設計與平台更新
*   **Liquid Glass 設計語言**：增加對複雜背景的擴散處理、邊緣變暗與高光效果。支援 macOS 27 的 `Show Borders` 設定。
*   **自動化適應性**：iOS App 在 iPad 與 Mac（iPhone Mirroring）上支援自由縮放，透過 `Trait Collections` 和 `Auto Layout` 自動適應多種螢幕尺寸。

### C. SwiftUI 與 Swift 語言
*   **SwiftUI 效能改進**：`State` 物件改為 Lazy 初始化（底層改為 macro），大幅減少重複初始化開銷。`AsyncImage` 內建 HTTP 快取。
*   **Swift 6.4**：強化編譯器診斷與語法簡化（如 `any Apple OS` 替代冗長的平台標記），並改進了 Type Checking 的效率與錯誤訊息。

---

## 3. 示範程式碼模式：Dynamic Profiles
透過 Dynamic Profiles，開發者可以定義多個行為模式，根據應用狀態切換：

```swift
// 使用結果建構子宣告 Profile
let session = LanguageModelSession(profile: {
    // 定義第一個情境：腦力激盪
    Profile("Brainstorming") {
        Modifier(.privateCloudCompute)
        Modifier(.temperature(0.8))
    }
    
    // 定義第二個情境： tutorial 生成
    Profile("TutorialGeneration") {
        Modifier(.privateCloudCompute)
        Modifier(.reasoningLevel(.deep))
    }
})
```
此模式允許在同一個 session 中，透過 `View Model` 狀態判斷動態切換執行策略，而無需中斷對話上下文。

---

## 4. 開發者的實用重點

### 開發者生產力工具（Xcode 27）
*   **Device Hub**：取代原有 Simulator，合併了模擬器與真實裝置的管理，支援直接從 Mac 操控物理裝置，並具備高保真的手勢模擬。
*   **Agentic Coding**：
    *   **Agent Client Protocol (ACP)**：開放性標準，開發者可引入任意相容的 AI 代理（如 Anthropic, OpenAI, Google）。
    *   **MCP (Model Context Protocol)**：Xcode 能夠透過此協定與 Figma、GitHub 等工具深度整合，直接實現「從設計稿到 UI 實作」的自動化。
*   **Xcode Cloud**：建置速度提升兩倍，全面支援 Vision Pro 與 Metal 應用。

### 關鍵行動建議
1.  **資源分發**：符合條件的開發者（App Store 下載量小於 200 萬次）可免費使用 Private Cloud Compute 的 Apple Foundation Models。
2.  **SwiftUI 遷移**：儘速將舊有自定義 UI 邏輯移轉至 SwiftUI，以獲取未來自動更新的 Liquid Glass 與 resizability 功能。
3.  **App 連結性**：優先實作 App Intents Schema，這不僅是 Apple Intelligence 的基石，也是未來 Siri 深度整合的關鍵。
4.  **Xcode 主題個人化**：利用 Xcode 27 的新主題系統（Theme system）優化長期開發體驗，並將設定同步至 iCloud。

---
*註：相關 API 與功能細節請參考官網釋出的 100+ 場技術 Session，並可透過 Apple Developer App 進行深入研讀。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/112/1/0e1d49e8-277b-49f9-aaff-d937c5956d86/downloads/wwdc2026-112_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/112/1/0e1d49e8-277b-49f9-aaff-d937c5956d86/downloads/wwdc2026-112_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/112/
