# Meet the Evaluations framework

這是一份針對 WWDC 2026 Session「Meet the Evaluations framework」的技術摘要。

---

# 技術摘要：Meet the Evaluations Framework

### 概述
在生成式 AI 與大型語言模型（LLM）驅動的應用程式中，傳統的單元測試（Unit Test）往往不足以驗證行為，因為相同的輸入可能會產生不同的輸出（機率性行為）。Apple 在 2026 年推出的 **Evaluations Framework**，旨在為開發者提供一套標準化、可擴展的系統，用以衡量智慧功能的品質。該框架協助開發者建立自動化測試管道，透過「Hill Climbing（爬山法）」疊代優化模型表現，確保應用程式的安全性與可靠性。

---

### 主要概念與框架核心
Evaluations Framework 提供了一組協議（Protocols）與類型，讓開發者能定義評估流程：

1.  **Model Sample**：代表評估的輸入單元，包含輸入資料與期望的輸出（預期結果）。
2.  **Metric（指標）**：分為兩大類：
    *   **定量指標（Quantitative Metrics）**：透過程式邏輯（如檢查陣列長度、特定關鍵字）來判斷結果。適用於規則明確的檢測。
    *   **定性指標（Qualitative Metrics）**：由 **Model Judges** 執行，利用更強大的模型（如 Private Cloud Compute）對輸出進行主觀評分與 rationale（分析理由）撰寫。
3.  **Evaluator**：執行具體評估的實體，負責對單一樣本進行測量，並回傳 Metrics。
4.  **Aggregate Metrics**：跨多個樣本的統計數據（如平均值、通過率），用以衡量整體趨勢。
5.  **Evaluation Protocol**：整合至 Swift Testing，開發者透過 `evaluates` trait 執行測試並生成評估報告。

---

### 關鍵程式碼模式

#### 1. 定義與執行評估
使用 `Evaluations` 框架定義評估邏輯，並透過 `Swift Testing` 執行：

```swift
// 1. 定義指標
let tagCountMetric = Metric("tag count") { output in
    // 檢查標籤數量是否在 3-8 之間
    return (3...8).contains(output.tags.count) ? .pass : .fail
}

// 2. 在 Swift Testing 中使用
@Test(.evaluates(myEvaluation, notes: ["config": "v1"]))
func testBookTagging() async throws {
    let results = try await EvaluationRunner.run(myEvaluation)
    let avg = results.aggregateValue(for: tagCountMetric)
    #expect(avg >= 0.8) // 要求 80% 的通過率
}
```

#### 2. Model Judges (模型評審)
當無法用簡單規則定義時，使用模型作為評審：

```swift
let relevanceJudge = ModelJudge(
    dimension: "Relevance",
    description: "標籤是否能準確描述書籍主題，而非讀者的個人情緒。",
    scale: [1: "完全不相關", 4: "高度相關"],
    model: .privateCloudCompute // 使用雲端強大模型
)
```

---

### 開發者實用重點與流程 (Evaluation-Driven Development)

該框架推廣「**評估驅動開發（Evaluation-Driven Development, EDD）**」方法論：

1.  **Hill Climbing（爬山法）**：
    *   先建立初步的指標與期望值。
    *   運行評估，發現效能瓶頸。
    *   調整模型的 Prompt 或設定（如 Guide 巨集）。
    *   重新運行，直到測試結果達成目標（通過率提升）。
2.  **從簡單開始**：
    *   初期使用 20-30 個樣本進行開發。
    *   若能用程式碼直接測量的，先使用定量指標；若涉及語意理解，才使用 Model Judges。
3.  **解決指標模糊問題**：
    *   若 Model Judge 的評分不穩定或不準確，應將「指標維度（Dimensions）」拆分（例如：將「品質」拆分為「相關性」與「實用性」）。
    *   **務必提供 Rationale**：要求模型 judge 提供評分理由，這是診斷模型錯誤最有效的工具。
4.  **合成資料**：
    *   當人工編寫測試案例不夠時，使用框架提供的 `Sample Generator` 合成大量多樣性的評估資料。

### 總結
Evaluations Framework 將「測試 AI」的工作標準化。對於致力於打造穩定、可靠 AI 應用的開發者而言，透過此框架建立自動化的評估循環，是降低 AI 系統不確定性、提升產品品質的核心戰略。建議開發者從手邊的智慧功能著手，先建立一個簡單的定量測試開始實踐。

<!-- resources -->

---

## 資源連結

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Designing datasets to test your feature](https://developer.apple.com/documentation/Evaluations/designing-evaluation-datasets)
- [Designing effective evaluations](https://developer.apple.com/documentation/Evaluations/designing-effective-evaluations)
- [Evaluating language model responses](https://developer.apple.com/documentation/Evaluations/evaluating-language-model-responses)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/298/5/0ffb7161-1edb-4e6f-872d-55be82c4402d/downloads/wwdc2026-298_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/298/5/0ffb7161-1edb-4e6f-872d-55be82c4402d/downloads/wwdc2026-298_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/298/
