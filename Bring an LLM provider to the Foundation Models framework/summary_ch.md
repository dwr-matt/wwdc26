# Bring an LLM provider to the Foundation Models framework

這份技術摘要將協助您快速掌握 Apple WWDC 2026 中關於 **Foundation Models framework** 的最新擴展。

---

# 技術摘要：Bring an LLM provider to the Foundation Models framework

### 概述
本次 Session 介紹了 Apple 將 **Foundation Models framework** 從單純提供 Apple 原生模型，轉型為一個開放式的 LLM 生態系統架構。開發者現在可以利用統一的 API，整合本地端（如 Core AI, MLX）或雲端（如 Private Cloud Compute, Anthropic, Google）的各種語言模型。Apple 透過一套標準化的「語言模型協定 (Language Model Protocol)」，讓應用程式能夠以一致的方式呼叫不同供應商的模型，實現高度的模組化與可攜性。

---

### 主要概念與框架架構

#### 1. 統一語言模型協定 (Language Model Protocol)
這是整個框架的核心。無論是 Apple 的系統模型、PCC（Private Cloud Compute）、Core AI 還是外部第三方模型，所有模型都必須遵循此協議，確保應用層的 API 使用體驗一致。

#### 2. 關鍵組件
*   **Language Model:** 定義模型的能力（Capabilities）與組態（Configuration）。
*   **Language Model Executor:** 負責實際執行模型的組件（如載入權重、處理串流請求、與推論引擎溝通）。
*   **Configuration:** 這是 Look-up Key，用來在執行期決定該使用哪個 Executor。框架會根據 Hashable 的 Configuration 快取 Executor，以優化效能並確保資源正確釋放。

#### 3. 處理流程
*   **Transcript（對話紀錄）:** 包含指令（Instruction）、使用者提示（Prompt）、工具呼叫（Tool calls）與模型回應。
*   **Translation 層:** Executor 作為橋樑，負責將框架的 `TranscriptEntry` 轉換為推論引擎能讀取的格式，並將輸出轉回 `Event` 串流。
*   **Response Events:** 包含文字 Delta、工具呼叫、Metadata 與自定義 Segment。

---

### 技術實現模式

#### 封裝模型 (Packaging)
Apple 建議使用 **Swift Package Manager**。模型提供者應將模型封裝為 Swift Package，並支援 iOS、macOS、visionOS、watchOS，甚至透過 Linux 支援伺服器端開發。

#### Executor 的生命週期與預熱 (Pre-warm)
*   **Pre-warm:** 開發者可以在執行請求前呼叫 `pre-warm`，讓 Executor 預先載入權重或建立網路連線，避免首發延遲。
*   **自動資源管理:** 當 Session 結束，框架會自動釋放 Executor，確保 DNNIT 執行、權重釋放與連線關閉，無需開發者手動編寫 teardown 邏輯。

#### 自定義擴展
*   **Response Metadata:** 輕量級地傳遞額外資訊（如 tokens/sec 或推論時間）。
*   **Custom Segments:** 當需要傳遞非文字類型（如音訊、影片或其他模態）時，可以定義自定義 Segment。這些 Segment 遵循 `PromptRepresentable`，可直接嵌入 Prompt 中。
*   **Server-side Tools:** 模型可以宣告並執行外部工具（如網路搜尋），Executor 負責串流這些工具的執行狀態與結果。

---

### 開發者實用重點

1.  **實作 API 時的友善性：**
    *   不要直接要求使用者傳入 API Key 字串。應提供 `TokenProvider` 或登入流程，並使用 **Keychain** 安全地儲存憑證。
    *   對於雲端模型，強烈建議實作 **Device Attestation**（設備認證），以防止惡意流量與篡改。

2.  **錯誤處理建議：**
    *   優先使用框架內建的 `LanguageModelError`（如 context overflow, rate limits, refusals）。
    *   僅在具備特定業務邏輯（如訂閱層級、帳戶狀態）失敗時，才定義自定義 Error 類型。

3.  **狀態同步與最佳化：**
    *   `Respond` 函數會接收完整的 `Transcript`。在處理多輪對話時，應比較新舊對話紀錄的差異，只處理新增的部分（Append-only），以最小化網路流量與計算資源損耗。

4.  **體驗設計：**
    *   盡早發送 `Metadata` 與 `Usage` 資訊，不要等到串流結束才讓開發者獲取成本與計費相關資訊。
    *   利用 `Dynamic Profiles` 來建構代理人（Agentic）體驗，透過框架的標準工具呼叫介面，提升應用功能的複雜度。

---

### 總結
Foundation Models framework 的開放標誌著 Swift 生態系統在 AI 整合上的重大轉折。模型供應商現在可以透過一套結構嚴謹的協定，讓數以百萬計的 Swift 開發者透過幾行程式碼即可調用您的模型，同時享受到 Apple 系統級的安全、隱私保障與硬體加速優化。

<!-- resources -->

---

## 資源連結

- [Foundation Models](https://developer.apple.com/documentation/FoundationModels)
- [Core AI Models](https://github.com/apple/coreai-models)
- [MLX Swift LM on GitHub](https://github.com/ml-explore/mlx-swift-lm)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/339/4/334f1ee9-4263-4c86-9b10-632f0f2edab1/downloads/wwdc2026-339_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/339/4/334f1ee9-4263-4c86-9b10-632f0f2edab1/downloads/wwdc2026-339_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/339/
