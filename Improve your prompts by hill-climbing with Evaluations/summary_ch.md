# Improve your prompts by hill-climbing with Evaluations

這份技術摘要整理自 WWDC 2026 Session「Improve your prompts by hill-climbing with Evaluations」，旨在協助開發者透過系統化的迭代流程（Hill-climbing），有效提升 AI 功能的輸出品質與評估準確度。

---

# WWDC 2026：使用評估（Evaluations）透過 Hill-climbing 優化 Prompts

## 概述
本課程深入探討如何在 Apple 的「Evaluations Framework」之上，建立一套科學化的迭代流程——**Hill-climbing（爬山法）**。開發者將學習如何透過「設定目標、執行評估、分析數據、調整變數」的循環，優化 AI 功能（如 BookTracker 的標籤生成服務）。此外，課程特別強調了「評估器漂移（Drift）」的問題，並展示如何透過統計指標（如 Cohen's Kappa）確保 AI 評分模型與人類標準保持一致。

---

## 主要概念與核心框架

### 1. Hill-climbing 迭代流程
這是一種系統化改善 AI 功能的開發循環：
*   **Development（開發）**：進行微小的變更（如修改 Prompt 或增加工具）。
*   **Evaluation（評估）**：運行測試，根據指標測量結果。
*   **Analyze（分析）**：深入檢查結果，理解模型表現與期望的落差。
*   **Science-driven（科學驅動）**：將每次變更視為「科學實驗」，一次只改變一個變數，以精確衡量該變數對結果的影響。

### 2. 評估漂移（Drift）與校準
當 AI 評分模型（Model Judge）的評分標準與人類專家逐漸背離時，稱為「漂移」。
*   **accuracy（準確度）**：簡單的比對模型與人類分數是否一致。
*   **Cohen's Kappa Coefficient**：更進階的統計指標，用來衡量兩個評分者（人類與 AI）的「一致性」。它扣除了純粹由運氣導致的隨機吻合，能更精準地評估 AI 評分器的可靠度。

### 3. Apple Evaluations Framework 關鍵組件
*   **Score Dimensions（評分維度）**：定義評估的指標（如：相關性 Relevance、實用性 Usefulness）。
*   **Model Judge Evaluator**：使用另一個 AI 模型來評分目標功能的輸出。
*   **Comparison View**：Xcode 提供的對比功能，可並排顯示兩次評估結果，利於觀察 Prompt 修改前後的差異。

---

## 示範的程式碼與技術模式

### 設定評估項目
開發者可以透過 `Swift Testing` 的 `expect` 巨集來定義評估門檻：
```swift
// 範例：定義期望的對齊分數
expect(alignmentScore >= 0.6) 
```

### 比較評估 (A/B Testing 概念)
為了驗證改進，應建立兩個評估對象：
1.  **Control Group（控制組）**：基於現有的 Prompt。
2.  **Experimental Group（實驗組）**：使用優化後的 Prompt 或新增工具。

### 優化 Prompt 的策略
*   **提供 Context（上下文）**：讓模型知道 App 的用途。
*   **提供 Few-shot Examples**：給予少量「高品質」的標籤範例，作為評分的基準。
*   **嚴格定義維度**：對 Relevance 或 Usefulness 提供具體的判定標準，例如「若標籤過於具體則給予低分」。

---

## 開發者的實用重點（Key Takeaways）

1.  **隔離變數（Isolate Variables）**：每次只修改一個維度（如 Prompt 中的一部分，或單一工具函數），避免難以釐清哪項變更導致了品質改善或下降。
2.  **評估你的評估器（Evaluate your Evaluators）**：定期檢查 Model Judge 的表現。如果 AI 評分器已經「漂移」，即便功能進步，你得到的數據也會是誤導的。
3.  **失敗的實驗價值極高**：不要排斥測試失敗，失敗的結果能幫助你了解模型在哪種情況下容易出錯，這對於後續除錯至關重要。
4.  **適度與過度擬合（Overfitting）**：在提供 Few-shot 範例時，給予「少量但精確」的範例即可。範例過多容易導致 AI 針對範例進行過度擬合，喪失在未知數據上的泛化能力。
5.  **工具輔助（Tools Integration）**：除了 Prompt 之外，透過引入外部工具（Tool Calling）獲取背景資訊（如書名、作者），往往比單純修改文字指令更能提升 AI 輸出品質。

---

*想進一步深入實作的開發者，建議同時參閱官方提供的 **"Meet the Evaluations Framework"** 以建立基礎管線，並參考 **"Create Robust Evaluations for Agentic Apps"** 以學習更進階的樣本生成與工具評估技術。*

<!-- resources -->

---

## 資源連結

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Designing effective model-as-judge evaluators](https://developer.apple.com/documentation/Evaluations/designing-effective-model-judges)
- [Designing specific, measurable criteria in an evaluation suite](https://developer.apple.com/documentation/Evaluations/designing-evaluation-criteria)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/335/4/a464d330-6aa2-456d-9a07-eae997aef08c/downloads/wwdc2026-335_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/335/4/a464d330-6aa2-456d-9a07-eae997aef08c/downloads/wwdc2026-335_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/335/
