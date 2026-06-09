# Build agentic app experiences with the Foundation Models framework

這是一份關於 Apple WWDC 2026 Session「Build agentic app experiences with the Foundation Models framework」的技術摘要。

---

# WWDC 2026 技術摘要：使用 Foundation Models 框架建構 Agentic App 體驗

## 概述
本場次介紹了如何利用 Apple 推出的新 API——**Dynamic Profiles（動態配置）**，讓開發者能夠在 Foundation Models 框架中建構具備「代理人（Agent）」能力的應用程式。隨著大型語言模型（LLM）應用場景日益複雜，本 session 聚焦於如何在多模型環境下進行上下文管理、邊界設定、以及高效的任務編排（Orchestration）。

---

## 主要概念與 API 介紹

### 1. Dynamic Profiles（動態配置）
Dynamic Profiles 是架構的核心，它定義了一個特定狀態下的模型行為。一個 Profile 包含：
*   **Instructions（指令）**：定義模型目標。
*   **Tools（工具）**：賦予模型執行的能力。
*   **Modifiers（修飾器）**：配置模型參數（如 Temperature、Sampling Mode、Reasoning Level 等）。

### 2. Dynamic Instructions
這是一種可重用的元件，允許開發者將一組工具與指令封裝在一起。它們是可組合（Composable）的，嵌套時會自動串接指令與工具，非常適合用於處理特定領域（如 Origami 專家知識）的邏輯。

### 3. Session Properties（會話屬性）
透過 `@SessionPropertyEntry` 巨集定義的狀態，可在不同的 Profile 與工具間共享。這些屬性是可變的，用於儲存如「對話摘要」等需要跨狀態持久化的資訊。

### 4. 工具調用模式（Tool Calling Mode）
新增了三種模式以增強對模型的控制：
*   `.allowed`（預設）：模型可自行決定是否使用工具。
*   `.disallowed`：禁止模型調用工具。
*   `.required`：強制模型使用工具（適合循環執行的 Agent 系統）。

---

## 程式碼模式與技術

### 1. 任務編排模式（Orchestration Patterns）
*   **BatonPass（接力棒）**：多個 Profile 共享同一個 transcript（對話記錄），透過調用工具修改變數來切換下一個處理的 Profile。適合需要連續協作的任務。
*   **Phone-a-Friend（電話求救）**：主 Profile 創建一個短暫的、隔離的子 Session 來詢問特定問題，獲取工具輸出後由父 Profile 進行最終彙整。適合複雜任務拆解。

### 2. 上下文管理與 Transcript 操作
*   **History Transform**：在進入 Profile 前對歷史記錄進行過濾或修剪，而不永久刪除上下文。
*   **Lifecycle Modifiers**：如 `onResponse`，允許在模型回應邊界執行 imperative code，例如更新摘要或 UI 狀態。
*   **錯誤處理策略**：可選擇 `revert`（預設）或 `preserve`（保留）transcript。若使用 `preserve`，開發者需自行管理 transcript 的狀態。

---

## 開發者實用重點

### 性能考量 (KV Cache)
*   **避免頻繁重寫歷史**：append-only 的操作能確保 KV Cache 的最佳化。頻繁刪除或修改 transcript 會導致 Cache 失效，增加延遲。
*   **儀表板診斷**：使用 Xcode 中升級後的 **Foundation Models Instrument** 來檢測 Cache 失效狀況。

### 準確性策略
*   **上下文陷阱**：在 Prompt 中混用過往歷史與新增工具時，可能導致模型困惑（例如：模型在沒有工具時習慣自行生成標題，加入工具後出現不一致行為）。
*   **數據驅動驗證**：務必使用 **Evaluations Framework** 建立測試集（Eval sets），量化調整 context engineering 策略後的實際影響。

### 工具與資源
*   **Foundation Models Framework Utilities**：官方釋出的開源 Swift 套件，內含實驗性模式與實用元件。
*   **Private Cloud Compute (PCC)**：對於需要高度隱私與專業知識的任務，建議優先選擇 PCC。

---

**總結建議：**
開發者應利用 `Dynamic Profiles` 進行「角色扮演」式的切換，並透過 `History Transform` 與 `Session Properties` 精細化控制 LLM 的上下文窗口。在建構複雜 Agent 時，優先考慮 `BatonPass` 或 `Phone-a-Friend` 設計，並務必透過 Instruments 與 Eval Framework 進行性能與準確性的嚴格測試。

<!-- resources -->

---

## 資源連結

- [Composing dynamic sessions with instructions and profiles](https://developer.apple.com/documentation/FoundationModels/composing-dynamic-sessions-with-instructions-and-profiles)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/242/4/7f05515d-be1a-43a0-9962-a1f77f115666/downloads/wwdc2026-242_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/242/4/7f05515d-be1a-43a0-9962-a1f77f115666/downloads/wwdc2026-242_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/242/
