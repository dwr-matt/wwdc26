# Design intuitive search experiences

這是一份針對 WWDC 2026 Session「Design intuitive search experiences」的詳細技術摘要。

---

# 技術摘要：設計直覺的搜尋體驗 (Design intuitive search experiences)

## 概述
本 session 由 Apple 設計團隊的 Rob 主講，旨在探討如何透過符合平台規範的設計模式，在 iOS、iPadOS 與 macOS 上打造高效且直覺的搜尋體驗。搜尋不僅是獲取資訊的工具，更是提升使用者對 App 滿意度與導航效率的關鍵。本場演講涵蓋了搜尋欄的標準化設計、跨平台擺放邏輯、搜尋優化策略（如建議與過濾），以及如何處理無結果的狀態。

---

## 主要概念與介面設計模式

### 1. 搜尋欄（Search Field）的核心元素
Apple 提供的搜尋組件包含以下預期互動：
*   **導航圖示：** 標誌性的放大鏡，清楚定義 UI 功能。
*   **佔位符文字 (Placeholder)：** 引導使用者輸入關鍵字。
*   **清除按鈕：** 輸入後出現，快速重置搜尋。
*   **取消按鈕：** iOS 在焦點獲取時出現，提供退出搜尋與關閉鍵盤的標準路徑。
*   **呈現樣式：** 系統會自動根據放置位置（如 Toolbar 或 Scroll view）適配 `Glass` 或標準樣式。

### 2. 跨平台搜尋放置策略
開發者應考慮「使用者導航方式」與「搜尋範圍（Scope）」來決定擺放位置：

*   **iOS 搜尋位置：**
    *   **底部工具列 (Bottom Toolbar)：** 最符合人體工學，適合頻繁操作的場景（如 Mail App）。點擊後欄位會上浮至鍵盤上方。
    *   **頂部工具列 (Top Toolbar)：** 當底部已有其他元件（如 Sheet）佔用時使用（如 Stocks App）。
    *   **搜尋分頁 (Search Tab)：** 針對大型應用，提供一個「統一搜尋入口」。可分為「標準分頁（用於探索）」與「顯著按鈕（用於快速存取）」。
    *   **內嵌搜尋 (Inline)：** 在特定檢視內容中直接搜尋（如 Apple Music Library），明確界定搜尋範圍。

*   **iPad 與 macOS 搜尋位置：**
    *   **工具列尾端：** 適合多欄位導航（Split View），如 Files 或 Notes。
    *   **側邊欄 (Sidebar)：** 用於過濾特定類別內容，如 Settings。
    *   **搜尋專屬標籤：** 提供沉浸式的大空間搜尋體驗。

---

## 關鍵技術與功能優化

### 1. 搜尋建議 (Search Suggestions)
*   **近期搜尋：** 顯示過往紀錄能顯著減少輸入成本。需提供個別移除（滑動手勢）與整批清除功能。
*   **預測性補全：** 在輸入時即時顯示結果，並透過視覺差異（如文字顏色）區分使用者輸入與系統補全內容。

### 2. 結果過濾與精煉
*   **Scope Bar：** 用於輕量級過濾（例如「全部郵件」與「當前郵件夾」）。
*   **搜尋 Token (Search Tokens)：** 這是強大的過濾工具，將關鍵字視覺化為「標籤」。使用者可透過 Token 進行多重篩選（如：搜尋「Joshua Tree」地點 +「2021」時間），這允許自然語言般的搜尋體驗。
*   **原則：** Token 應與其他過濾 UI（如 Scope Bar）並存，不應取代具備高可發現性的 UI 組件。

### 3. 處理無結果狀態 (Empty State)
*   請務必使用系統提供的 **Content Unavailable View**，包含搜尋圖示、標題與副標題。
*   建議在無結果頁面顯示使用者剛才輸入的查詢內容，幫助使用者快速發現輸入錯誤。

---

## 開發者的實用重點總結

1.  **一致性為上：** 若使用自定義 Icon，必須外觀上極度接近系統的「放大鏡」符號，以維持認知的一致性。
2.  **人體工學至上：** 在 iOS 上，盡可能將搜尋欄放在手指觸手可及的底部工具列。
3.  **範圍清晰：** 透過搜尋欄的擺放（如側邊欄 vs 內嵌）來告知使用者「你正在搜尋什麼範圍的資料」。
4.  **動態調整：** 搜尋欄在 Toolbar 中的寬度應具備響應式，當欄位空間不足時，應能自動收合為搜尋按鈕。
5.  **善用工具資源：** 務必參閱 Apple 的 **Human Interface Guidelines (HIG)**，並利用內建的系統元件（如 `UISearchController` 等）而非從零打造，以確保系統級互動（如鍵盤處理、動畫流暢度）的一致性。

---
*編輯備註：本 session 強調了「減少輸入」與「提高可見度」，在開發時請優先評估搜尋功能的「上下文關聯性」。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/292/5/05adbfdf-d9ba-4a6d-8d2f-f43593907f55/downloads/wwdc2026-292_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/292/5/05adbfdf-d9ba-4a6d-8d2f-f43593907f55/downloads/wwdc2026-292_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/292/
