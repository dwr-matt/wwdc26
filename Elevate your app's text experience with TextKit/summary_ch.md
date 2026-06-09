# Elevate your app's text experience with TextKit

這是一份為開發者整理的 WWDC 2026 Session **"Elevate your app's text experience with TextKit"** 技術摘要。

---

# WWDC 2026 技術摘要：使用 TextKit 提升 App 的文字體驗

## 概述
本 Session 深入探討了 Apple 的 TextKit 架構，特別著重於解決開發者在「框架提供的預設 TextView（便利性）」與「完全自訂的文字引擎（靈活性）」之間面臨的兩難。講者演示了如何透過最新的 TextKit API 擴充現有的 `UITextView` 與 `NSTextView`，讓開發者在享受框架既有功能（輸入、撤銷、無障礙支援）的同時，也能實現高度客製化的文字介面。

---

## 核心概念與架構

TextKit 採用四層架構，確保了高效的文字佈局與渲染：

1.  **TextStorage 層**：封裝文字資料（預設使用 `NSTextContentStorage`）。
2.  **Layout 層**：將文字轉換為「佈局片段」（`NSTextLayoutFragment`）。
3.  **Viewport 層**：追蹤哪些文字片段目前位於螢幕可見範圍內。
4.  **View 層**：負責最終的視覺呈現（如 `UITextView` 或 `NSTextView`）。

### 關鍵元件
*   **`NSTextViewportLayoutController` (ViewportController)**：協調佈局管理器與檢視層，確保只有可見區域的文字被渲染，大幅提升效能。
*   **Viewport Layout Process**：當滾動、編輯或選取發生時，觸發的生命週期流程。

---

## 新增 API 與技術擴充

本次更新重點在於提升檢視層的控制力：

### 1. 視覺渲染介面 (`NSTextViewportRenderingSurface`)
為了彌補過去 TextKit 無法精確追蹤渲染目標的缺陷，新增了兩組協定：
*   **`NSTextViewportRenderingSurface`**：代表 Viewport 內可繪製的視覺元素（View 或 Layer）。
*   **`NSTextViewportRenderingSurfaceKey`**：識別符（如 `NSTextLayoutFragment`），用於快取渲染表面，優化效能。

### 2. 擴充現有的 TextView
現在 `UITextView` 與 `NSTextView` 正式遵循 `NSTextViewportLayoutControllerDelegate`，開發者可以透過子類化（Subclassing）來攔截佈局週期：
*   `willLayout()`：開始計算佈局前的清理或初始化。
*   `configureRenderingSurface()`：處理具體的渲染配置。
*   `didLayout()`：獲取佈局資訊後的處理（如繪製行號）。

### 3. 文字附件重用策略 (`NSTextAttachmentViewProvider`)
針對包含圖片或動畫的附件，新增了 `register(for:reusePolicy:)` 機制：
*   **`onEditingInlineParagraphs`**：防止編輯文字時附件視圖被重建（解決動畫重啟問題）。
*   **`onScrollingOutOfViewport`**：當附件滾出畫面時快取資源，滾回時復原，提升效能。

---

## 開發者實作模式（Demo 案例）

### 範例：實現程式碼編輯器的「行號」功能
開發者可以透過以下步驟擴展 `UITextView`：
1.  **狀態維護**：在 subclass 中建立陣列儲存片段的邊界（Bounds）。
2.  **攔截週期**：
    *   在 `willLayout` 中計算當前 viewport 起始的行號（使用 `enumerateTextElementsFromTextLocation`）。
    *   在 `configureRenderingSurface` 中累加該段落的 Y 軸座標資訊。
    *   在 `didLayout` 中將收集到的邊界傳遞給容器視圖（Container View）進行繪製。

### 範例：可折疊文字區塊
透過遵循 `NSTextContentStorageDelegate`：
*   使用 `shouldEnumerate` 方法來篩選不需要渲染的段落（即「折疊」狀態）。
*   利用 `Set<Int>` 紀錄被折疊的段落 ID，並根據使用者點擊事件觸發 `layoutManager` 更新。

---

## 開發者實用重點（Key Takeaways）

*   **選擇路徑**：
    *   **追求開發速度**：直接使用 `UITextView` / `NSTextView`，並利用新的代理方法進行擴充。
    *   **追求極致自訂**：若框架視圖無法滿足需求，可建構自定義的 TextKit Pipeline。
*   **效能優化**：務必使用 `NSTextViewportLayoutController` 來處理視圖更新，不要在非可見區域執行昂貴的佈局計算。
*   **狀態保存**：利用新增的 `reusePolicy`，避免因為文字編輯（導致佈局重新計算）而意外拆除或重啟您的附件 UI 組件。
*   **多呈現模式**：如果需要在不同視圖呈現同一份文字，可以將多個 `NSTextLayoutManager` 連接到同一個 `NSTextContentStorage`，資料會自動保持同步。

建議開發者下載隨附的 **Sample App**，觀察這些 delegate 方法如何優雅地處理複雜的文字排版邏輯。

<!-- resources -->

---

## 資源連結

- [Enriching your text in text views](https://developer.apple.com/documentation/UIKit/enriching-your-text-in-text-views)
- [TextKit](https://developer.apple.com/documentation/AppKit/textkit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/370/5/f61dbe38-7302-451a-b3ab-9851d5746315/downloads/wwdc2026-370_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/370/5/f61dbe38-7302-451a-b3ab-9851d5746315/downloads/wwdc2026-370_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/370/
