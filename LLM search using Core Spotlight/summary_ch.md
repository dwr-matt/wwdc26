# LLM search using Core Spotlight

這是一份關於 WWDC 2026 Session「LLM search using Core Spotlight」的詳細技術摘要。

---

# 技術摘要：利用 Core Spotlight 實現 LLM 搜尋

### 概述
本 Session 介紹了如何將 Apple 的 **Core Spotlight** 與 **Foundation Models 框架** 結合，為應用程式打造具備情境感知的對話式搜尋體驗。開發者不再需要手動撰寫複雜的搜尋查詢，而是透過引入新的 `SpotlightSearchTool`，讓大型語言模型（LLM）能直接存取 App 內的索引資料，進行推理並產生精確的內容回應。

---

### 主要概念與框架

#### 1. Spotlight Search Tool
這是本次引入的核心工具，遵循 `Tool` 協議。它允許模型對 App 的 Core Spotlight 索引執行搜尋，並將搜尋結果作為上下文（Context）用於生成回應。
*   **支援平台**：iOS, iPadOS, macOS, visionOS。
*   **運作機制**：模型根據用戶提問決定是否呼叫工具 -> 生成查詢參數 -> Spotlight 執行查詢 -> 返回結果 -> 模型基於結果生成最終回應。

#### 2. Foundation Models Framework (Tool Calling)
透過工具呼叫（Tool Calling），模型不僅能回答通用問題，還能透過 App 提供的工具獲取私有資料。這包括宣告工具的參數、執行指令及處理輸出。

#### 3. 評估框架 (Evaluations Framework)
為了確保 AI 回應的準確性，Apple 提供了評估工具來測試：
*   模型呼叫工具的效率與準確度。
*   基於特定資料集的回應品質（Result Coverage）。
*   支援透過 `ModelSample` 協議與 `Sample Generation API` 進行自動化測試。

---

### 技術實作模式

#### 設定 Spotlight Search Tool
開發者只需匯入 `CoreSpotlight` 與 `FoundationModels`，並將工具加入語言模型 Session：

```swift
// 設定工具
let searchTool = SpotlightSearchTool(configuration: .default)

// 加入模型 Session
let session = LanguageModelSession()
session.addTool(searchTool)
```

#### 處理 metadata 恢復 (Index Delegate)
由於 Spotlight 為了效能，部分文字內容會以壓縮格式儲存，模型可能無法直接讀取。需實作 `CSSearchableIndexDelegate` 來恢復完整的 `CSSearchableItem`：

```swift
func searchableItems(for identifiers: [String]) async throws -> [CSSearchableItem] {
    // 根據 ID 返回完整的項目，包含額外的 metadata 供模型推理
}
```

#### 自定義 Pipeline Stages (進階推理)
若搜尋需求複雜（如：計算平均值、 sentiment analysis），可自定義 Pipeline Stage：
1.  **定義 Stage**：接收 `CSSearchableItem` 並執行計算。
2.  **註冊 Stage**：將其加入 `SpotlightSearchTool` 配置中。
3.  **生成式執行**：模型會根據需求，動態決定是否觸發自定義計算邏輯。

---

### 開發者實用重點

1.  **資料捐贈是基礎**：在使用 `SpotlightSearchTool` 前，必須先確保 App 已經妥善透過 `CSSearchableIndex` 捐贈 searchable content。
2.  **善用 Guidance Profiles**：若模型 Context Size 有限（特別是 On-device 模型），應使用 `GuidanceProfile` 來限制模型搜尋的屬性範圍（例如：僅搜尋「日期」與「地點」）。
3.  **非同步回應處理**：搜尋結果會以 `AsyncSequence` (Search Replies) 的形式返回。開發者應使用 `queryToken` 來監控搜尋進度，確保 UI 能即時更新。
4.  **利用 Contact Resolver**：若 App 涉及個人關係（如：同行者），使用 `ContactResolver` 能幫助模型將查詢條件精確匹配到對應的聯絡人資料。
5.  **測試與迭代**：建議使用 JSON 定義包含 Query、預期 Item ID 以及預期回應的樣本集，利用 Xcode 測試目標進行持續整合，確保 AI 回應品質不會隨索引資料變動而下降。

---

**編輯結語：**
這次更新的核心理念是「**從撰寫搜尋查詢轉向提供內容上下文**」。透過 Spotlight Search Tool，Apple 成功將繁雜的檢索邏輯抽象化，讓開發者能專注於如何豐富 App 的索引資料，而非處理搜尋語法。建議開發者從現有的 `CSSearchableIndex` 整合開始，逐步引入 Tool Calling 來提升體驗。

<!-- resources -->

---

## 資源連結

- [Spotlight search tool](https://developer.apple.com/documentation/CoreSpotlight/Spotlight-search-tool)
- [Making your indexed content available to Foundation Models](https://developer.apple.com/documentation/CoreSpotlight/making-your-indexed-content-available-to-foundation-models)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/246/4/b390ab9d-d231-4cf5-9d1b-e4270ef5012b/downloads/wwdc2026-246_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/246/4/b390ab9d-d231-4cf5-9d1b-e4270ef5012b/downloads/wwdc2026-246_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/246/
