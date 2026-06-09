# Best practices for integrating visual intelligence in your app

這是一份為開發者準備的 WWDC 2026 Session **「Best practices for integrating visual intelligence in your app」** 技術摘要。

---

# 技術摘要：將 Visual Intelligence 整合至您的應用程式

本場次詳細介紹了如何透過 Apple 的 **Visual Intelligence (視覺智慧)** 框架，讓使用者的相機或截圖與您的 App 產生連結。開發者不僅能透過 App 顯示相關內容（如音樂專輯、音樂會資訊），還能透過系統儲存整合（System Store Integrations）獲取使用者透過視覺智慧辨識後儲存的數據。

---

## 一、 核心概念與運作流程

Visual Intelligence 的整合主要分為兩大類：
1.  **提供搜尋結果（Image Search）**：當使用者對影像進行辨識時，您的 App 能提供深度連結或相關資訊。
2.  **存取系統數據（System Store Integrations）**：當使用者透過視覺智慧辨識並儲存資訊（如聯絡人、行事曆、健康數據）後，您的 App 可透過標準框架同步這些資料。

---

## 二、 主要框架與 API

*   **App Intents**：核心骨架。用於定義 App 實體（Entities）、查詢邏輯（Queries）與導航行為（Open Intents）。
*   **Vision Framework**：處理影像辨識的核心。透過 `generateImageFeaturePrintRequest` 將影像轉換為「特徵指紋（Feature Print）」，以便在本地進行高效率的影像相似度比對。
*   **System Stores**：
    *   **EventKit**：讀取行事曆事件。
    *   **Contacts**：存取辨識出的聯絡人資訊。
    *   **HealthKit**：獲取醫療裝置讀數。

---

## 三、 技術實作模式

### 1. 定義 App Entities (App 實體)
透過 `AppEntity` 定義您想要呈現的內容，並設定 `DisplayRepresentation`。
*   **建議**：圖片應提供縮圖 (Thumbnail) 而非完整解析度資源，以優化載入速度與顯示效果。
*   **視覺呈現**：Title 與 Subtitle 限制約三行文字，請確保最重要的辨識資訊置於開頭。

### 2. 實作 Intent Value Query
實作 `IntentValueQuery` 協議，系統會傳入 `SemanticContentDescriptor`（包含 pixel buffer）。
*   **流程**：
    1. 從 `SemanticContentDescriptor` 取得 pixel buffer。
    2. 使用 Vision Framework 產生 Feature Print。
    3. 與應用程式內預先計算好的本地資料庫進行相似度比對。
    4. 依據相似度排序並過濾掉不相關結果。

### 3. 處理多型資料 (Union Values)
若您的 App 需要同時提供多種類型結果（如：「專輯」與「演唱會資訊」）：
*   定義一個 `UnionValue` enum。
*   更新 Query 以回傳該 Union 類型，並分別實作對應的 `OpenIntent`。

### 4. 實作 Semantic Content Search
若辨識結果無法直接對應，或使用者想深入瀏覽，應實作 `SemanticContentSearch` 協議：
*   **目的**：引導使用者進入 App 內完整的搜尋介面。
*   **優化**：可利用傳入的 context，在 App 內自動填入預設搜尋條件，縮短使用者路徑。

---

## 四、 開發者實用重點與最佳實作 (Best Practices)

*   **跨平台考量**：
    *   同套程式碼可於 iOS、iPadOS 與 macOS 上運作。
    *   **差異點**：iOS 常針對實體拍攝，macOS/iPad 則多為截圖。需考量 macOS 上輸入的圖像 buffer 可能較大，適時進行 resize。
*   **效能優化**：
    *   **預先計算 (Pre-computing)**：務必預先計算好目錄中的 Feature Prints，避免在查詢當下進行昂貴的運算。
    *   **輕量化**：`OpenIntent` 的 `perform` 方法執行時 App 即將進入前景，應保持輕量，將複雜的資源載入延遲到 View 出現後再執行。
*   **排序優先級**：務必將最相關的結果排在第一位，系統會根據多個提供者的結果進行排序顯示。
*   **系統儲存自動整合**：若您的 App 本身已採用 EventKit 或 HealthKit，只需監聽對應的儲存庫更新，即可自動取得 Visual Intelligence 寫入的資訊，無需額外開發針對該功能的同步邏輯。

---

**總結建議**：Visual Intelligence 是擴展 App 發現機制（Discovery）的強大工具。建議從定義簡單的 `AppEntity` 開始，並結合現有的 App Intents 與 Vision Framework，將您的 App 深度嵌入到系統層級的影像辨識流程中。

<!-- resources -->

---

## 資源連結

- [Integrating your app with visual intelligence](https://developer.apple.com/documentation/VisualIntelligence/integrating-your-app-with-visual-intelligence)
- [Visual Intelligence](https://developer.apple.com/documentation/VisualIntelligence)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/297/5/25343020-b502-4808-967a-6f6460789dc2/downloads/wwdc2026-297_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/297/5/25343020-b502-4808-967a-6f6460789dc2/downloads/wwdc2026-297_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/297/
