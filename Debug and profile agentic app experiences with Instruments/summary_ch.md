# Debug and profile agentic app experiences with Instruments

這是一份針對 WWDC 2026 Session「Debug and profile agentic app experiences with Instruments」的技術摘要。

---

# 技術摘要：使用 Instruments 調試與剖析 Agentic App 體驗

## 概述
隨著 Foundation Models 框架的普及，開發者能夠輕鬆將生成式 AI 整合至應用中。然而，由於 LLM（大型語言模型）具備**非確定性（Non-deterministic）**的特性，傳統的單元測試與除錯方法往往失效。本 Session 介紹了如何利用 Xcode 內建的 **Instruments** 工具（特別是全新的 **Foundation Models Instrument**），來觀察模型的思維鏈（Chain of Thought）、排查代理（Agentic）行為中的邏輯錯誤，並優化應用程式的效能與延遲。

---

## 主要概念與框架介紹

### 1. 代理型應用開發的三大挑戰
*   **機率性輸出（Probabilistic Output）：** 同樣的 Prompt 可能產生不同結果，傳統斷言（Assertion）測試無法適用，必須轉向評估回應的「品質」與「意圖」。
*   **模型間通訊（Model-to-model communication）：** 複雜任務（如先識別圖片再生成教學）依賴多個模型串接，資料流轉的穩定性與錯誤處理是開發難點。
*   **可觀察性（Observability）：** 當流水線發生故障時，必須能清楚追蹤「模型收到了什麼？」、「模型做了什麼決定？」以及「為什麼這樣決定？」。

### 2. Foundation Models Instrument 的核心功能
該工具提供視覺化的時間軸與層級樹狀檢視（Tree View），幫助開發者掌握：
*   **Instruction 軌道：** 監控當前指令集與工具集的啟動時間與切換節點。
*   **Model Inference 軌道：** 區分 Prompt 處理時間（黃色）與回應生成時間（橙色）。
*   **樹狀檢視（Tree Detail View）：** 提供完整的層級結構（Session > Requests > Model Inferences > Instructions > Prompts > Responses），可深入檢視單一呼叫的詳細資訊。

---

## 示範技術與調試模式

### 調試流程示範
講師示範了一個「手作輔助 App」的開發場景，並透過 Instruments 發現「無法切換至教學模式」的靜默失敗（Silent Failure）問題：
1.  **開啟 Profiling：** 透過 Xcode 選擇 Profile，並選擇 Foundation Models 模板。
2.  **分析 Timeline：** 發現整個 Session 只有一套指令集執行，與設計的「腦力激盪模式」切換「教學模式」不符。
3.  **檢視 Inspector：** 在模型推論節點中，查看 Prompt 與指令集定義，發現雖然 Prompt 提到工具，但該指令集定義（Dynamic Instructions）中漏掉了 `switch to tutorial mode` 工具的配置。
4.  **修正代碼：** 在 Xcode 中補全指令定義中的 `tools` 陣列，重新執行後即可見到指令集隨任務需求正確變更。

---

## 開發者的實用重點

### 1. 效能優化指標
Instruments 提供了關鍵的效能數據，幫助開發者進行基準測試（Benchmarking）：
*   **Time to First Token (TTFT)：** 反映系統接收 Prompt 後開始回應的反應時間，若過長建議簡化 Prompt。
*   **Tokens per Second (TPS)：** 衡量生成速度，適合用來比較不同配置的效能差異。
*   **Total Latency：** 使用者感受到的總等待時間，建議使用**串流（Streaming）**技術來減少感知延遲。

### 2. 除錯技巧建議
*   **善用 Info 欄位：** 在樹狀檢視中，優先關注被標記的錯誤、異常長度或異常 Token 數量的節點。
*   **安全性注意：** 工具會記錄包含敏感資訊的 Prompt 與回應，請確保 trace 檔案的安全。
*   **閉環驗證：** 透過 Instruments 確認切換指令集後，上下文（Context）是否有正確傳遞給下一個模型推論。

### 3. 進階建議
*   **評估框架（Evaluations Framework）：** 建議在完成除錯後，觀看《Meet the Evaluations framework》Session，以學習如何透過結構化評估來提升 Prompt 品質。
*   **版本需求：** 請使用 Xcode 27 與最新 OS 版本以獲得完整的工具支援。

---

**結語：** 
當開發 Agentic 應用時，Instruments 不僅是一個效能分析器，更是你通往模型內部邏輯的「透視鏡」。透過視覺化的資料流轉與即時的指標監控，開發者能更有信心地打造穩定且高品質的智慧型體驗。

<!-- resources -->

---

## 資源連結

- [Analyzing the runtime performance of your Foundation Models app](https://developer.apple.com/documentation/FoundationModels/analyzing-the-runtime-performance-of-your-foundation-models-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/243/4/127c397a-8124-4f3d-ad18-ac2a1d275803/downloads/wwdc2026-243_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/243/4/127c397a-8124-4f3d-ad18-ac2a1d275803/downloads/wwdc2026-243_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/243/
