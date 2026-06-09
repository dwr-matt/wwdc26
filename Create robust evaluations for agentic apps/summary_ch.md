# Create robust evaluations for agentic apps

這份技術摘要旨在協助開發者掌握 Apple WWDC 2026 中關於「建立穩健 Agentic App 評估機制」的核心知識。

---

# 技術摘要：為 Agentic Apps 建立穩健的評估機制

## 1. 概述
本場 Session 介紹了 Xcode 27 中強化的 **Evaluations Framework**。隨著 Agentic（代理）應用程式的複雜度增加，開發者面臨數據集規模小、邊緣案例難以覆蓋、以及工具呼叫（Tool Calling）行為難以追蹤的挑戰。本演講重點在於如何透過「合成數據生成（Synthetic Data Generation）」擴大測試覆蓋率，以及如何透過「軌跡期望（Trajectory Expectations）」精確評估模型在使用工具時的邏輯正確性。

---

## 2. 主要概念與框架介紹

### A. 解決數據集覆蓋率：合成數據生成
單靠手寫少量測試樣本無法反映真實世界的複雜度。框架提供 API 讓開發者透過程式碼定義生成管線，利用模型自動生成多樣化的測試數據。
*   **關鍵目標：** 從「數量」轉向「覆蓋率（Coverage）」。
*   **優勢：** 快速擴充數據集，模擬真實使用者多變的輸入方式。

### B. 評估代理行為：工具呼叫（Tool Calling）評估
當應用程式涉及多步驟鏈式操作（例如：搜尋書本 → 獲取詳情 → 推薦相似書籍），僅驗證最終輸出結果是不夠的。開發者需要評估模型「如何」達成任務，包含工具的選擇、參數的準確度，以及呼叫順序。

---

## 3. 關鍵技術與程式碼模式

### 使用 `Make Samples` 生成合成數據
`Make Samples` API 需要三個核心組件：`Prompt`（指令）、`Dataset`（起始數據集）、`Target Count`（目標數量）。

```swift
// 程式碼範例：定義合成數據產生器
let generator = SampleGenerator(
    prompt: generationPrompt,
    dataset: initialSamples,
    targetCount: 100
)

// 使用自定義配置
generator.sessionProvider = {
    // 定義使用特定的模型與系統指令
    LanguageModelSession(model: .privateCloudCompute, instructions: "...")
}

// 執行生成
let expandedDataSet = try await generator.run()
```

### 驗證機制 (Validator)
透過 `validator` 閉包，開發者可以定義規則來篩選生成結果，自動拋棄不符合格式或邏輯的樣本。
*   **檢查維度：** 內容長度、標籤數量、屬性格式（例如必須全小寫）。

### 軌跡期望 (Trajectory Expectations)
這是評估工具呼叫的核心。它不僅驗證「有無呼叫」，更驗證「執行過程」。

```swift
// 範例：定義軌跡期望
let expectation = TrajectoryExpectation(
    // 檢查模型是否按順序呼叫特定工具
    steps: [
        .call(name: "searchBooks", arguments: ["tag": "Gothic"]),
        .call(name: "getBookDetails")
    ],
    // 透過 matcher 進行靈活比對（不只是字串相等）
    matchers: [.naturalLanguage("Gothic")]
)
```
*   **重要參數：**
    *   `unordered`: 若設定為 true，則表示不限制呼叫先後順序。
    *   `disallowed`: 指定模型「不應該」呼叫的工具，確保安全性與邏輯邊界。

---

## 4. 開發者實用重點

1.  **Iterative Process（迭代過程）：** 合成數據不是一次性的。建議遵循「定義初始集 → 生成 → 驗證 → 分析 → 補充邊緣案例」的循環。
2.  **上下文視窗限制：** 使用 `sessionProvider` 時，若模型輸出過大導致上下文耗盡，框架會自動重啟 Session。請確保你的 `sessionProvider` 包含自包含（self-contained）的指令，以便在沒有前一筆紀錄的情況下也能運作。
3.  **評估報告：** 利用 Xcode 27 的新版 Evaluation Report，可以直觀地比較不同 dataset（如 13 個 vs 100 個樣本）之間的評分差異。若分數下降，通常意味著舊版測試太簡單，無法揭露真實性能。
4.  **檢查順序的重要性：** 代理應用程式中，「順序」即「邏輯」。若模型在沒有取得 ID 的情況下就去呼叫 `getBookDetails`，這即是典型的 Bug，唯有軌跡評估能捕捉此類問題。
5.  **合成測試數據的生成：** 也可以利用框架為「工具評估」生成合成數據，但務必在 Prompt 中明確定義工具的功能與期望順序，否則生成的樣本將無法作為正確的評估基準。

---
*編輯建議：建議開發者從簡單的單元測試開始，並配合 Xcode 的 `Compare` 功能，定期檢視模型在擴大數據集後的效能退化情況，這對於開發可靠的 AI 功能至關重要。*

<!-- resources -->

---

## 資源連結

- [Book Tracker: Using Evaluations to evaluate an intelligent feature](https://developer.apple.com/documentation/Evaluations/book-tracker-using-evaluations-to-evaluate-an-intelligent-feature)
- [Generating synthetic datasets](https://developer.apple.com/documentation/Evaluations/generating-synthetic-evaluation-datasets)
- [Evaluating tool-calling behavior](https://developer.apple.com/documentation/Evaluations/evaluating-tool-calling-behavior)
- [Scoring with model-as-judge evaluators](https://developer.apple.com/documentation/Evaluations/scoring-with-model-as-judge-evaluators)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/299/4/ef9fbc06-fc78-4896-9848-0f0fe2e75fb9/downloads/wwdc2026-299_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/299/4/ef9fbc06-fc78-4896-9848-0f0fe2e75fb9/downloads/wwdc2026-299_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/299/
