# What's new in the Foundation Models framework

這份技術摘要整理自 WWDC 2026《What's new in the Foundation Models framework》議程，為您重點解析 Foundation Models Framework 在 2027 年版的核心升級。

---

# WWDC 2026 技術摘要：Foundation Models Framework 新功能全解析

## 概述
本 session 全面介紹了 Foundation Models Framework 的重大升級。重點在於 OS 層級的深度整合、更豐富的模型支援、以及全新的「代理（Agentic）」開發原語。最引人注目的消息是該框架將**全面開源**，並推出 `Foundation Models Framework Utilities`，讓開發者能更靈活地運用實驗性 API 與開源工具。

---

## 主要概念與 API 介紹

### 1. 模型升級與多模態能力
*   **全新 On-Device 模型**：從零構建，大幅提升邏輯推理與工具調用（Tool Calling）能力。
*   **視覺化能力（Vision）**：iOS 27 支援將圖像直接作為提示詞（Prompt）的附件。支援 `UIImage`、`NSImage`、`CGImage`、`CoreVideo Pixel Buffers` 及檔案 URL。
*   **Private Cloud Compute (PCC) 模型**：
    *   專為高負載任務設計，擁有 32,000 token 的上下文窗口（Context Window）。
    *   **推理能力（Reasoning）**：支援 `reasoningLevel` 參數，讓模型在回答前進行深思，顯著提升複雜任務的準確度。
    *   **隱私與便捷性**：無需繁瑣的帳號與 API 金鑰管理，完全整合於 Apple Intelligence 生態，並具備可驗證的隱私保護。

### 2. 模型抽象層（Model Abstraction Layer）
框架現在允許透過統一的 `LanguageModel` 協議整合幾乎任何 LLM。
*   **現有支援**：System Language Model、PCC。
*   **新增支援**：Core AI Language Model、MLX Language Model（針對 Apple Neural Engine 與 GPU 優化）。
*   **第三方整合**：Anthropic 和 Google 透過 Swift Package 提供原生模型支援。

### 3. 系統級工具（System Tools）
*   **Vision 整合**：新增 `Barcode Reader` 與 `OCR` 工具，增強模型對視覺資訊的結構化提取能力。
*   **Spotlight RAG**：正式引入由 Spotlight 驅動的檢索增強生成（RAG）工具，實現完全離線的個人知識庫檢索。

### 4. Dynamic Profiles (代理原語)
這是建構「代理式（Agentic）」體驗的核心：
*   定義：透過 `DynamicProfile` 協議，讓開發者在同一個 Session 中動態切換指令（Instructions）、工具（Tools）與模型配置。
*   場景：例如從「分析模式」切換到「腦力激盪模式」，框架會自動處理上下文的遷移與模型參數調整。

---

## 技術模式與實作指南

### 程式碼模式：使用 Dynamic Profiles
開發者可以透過 `DynamicProfile` 狀態管理來動態變更模型的行為：

```swift
// 示意：定義不同的模式配置
struct MyProfile: DynamicProfile {
    var body: some Profile {
        if mode == .analysis {
            Instructions("分析圖片中的手工藝細節")
            Tools(AnalysisTool())
        } else {
            Model(PCCModel()) // 切換到更強大的 PCC 模型
            ReasoningLevel(.deep)
            Instructions("基於分析結果提出創意專案")
        }
    }
}
```

### 使用第三方模型
透過 Swift Package Manager 匯入後，初始化變得非常簡潔：
```swift
let thirdPartyModel = AnthropicModel(apiKey: secureKeychain.fetch())
let session = LanguageModelSession(model: thirdPartyModel)
```

---

## 開發者實用重點

1.  **監控 Token 用量**：使用 `session.usage` 屬性追蹤 Input/Output token，以及 reasoning 消耗的 token 數量，便於管理成本與效能。
2.  **安全性規範**：嚴禁將 API 金鑰寫入程式碼。使用 OAuth 機制獲取 Token 並存儲於 **Keychain**。
3.  **評估框架（Evaluations Framework）**：語言模型具有不可預測性，建議導入 Evaluations Framework 來量化測試提示詞調整後的統計影響，確保產出品質。
4.  **CLI 與 Python SDK**：
    *   `FM CLI`：允許在終端機直接測試模型或嵌入 Shell 腳本進行自動化處理。
    *   `Python SDK`：針對數據科學家與研究人員，提供與 Swift 版本同等強大的本地模型調用介面。
5.  **watchOS 支援**：2027 年起，watchOS 開發者可透過 PCC 調用強大的運算模型。

---
*建議進一步觀看的相關影片：*
*   *Building with Private Cloud Compute*
*   *Bring an LLM Provider to the Foundation Models Framework*
*   *Build agentic app experiences with foundation models*

<!-- resources -->

---

## 資源連結

- [Expanding generation with tool calling](https://developer.apple.com/documentation/FoundationModels/expanding-generation-with-tool-calling)
- [Analyzing images with multimodal prompting](https://developer.apple.com/documentation/FoundationModels/analyzing-images-with-multimodal-prompting)
- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/241/6/900558cb-1997-490a-9aac-2461b209e578/downloads/wwdc2026-241_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/241/6/900558cb-1997-490a-9aac-2461b209e578/downloads/wwdc2026-241_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/241/
