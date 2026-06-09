# Rediscover the HTML select element

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 關於 `select` 元素客製化的最新進展。

---

# 技術摘要：重新發掘 HTML `select` 元素 (Rediscover the HTML select element)

## 概述
長久以來，網頁開發者若想自訂 `<select>` 下拉選單的樣式，往往被迫放棄原生的語意化標記，轉而使用龐大的 JavaScript 函式庫或堆疊大量的 `<div>` 來模擬。WWDC 2026 推出了「可客製化選單」（Customizable Select）功能（自 Safari 27 與 Chrome 135 起支援），讓開發者能保留原生的無障礙存取能力（Accessibility）與語意，同時實現高度靈活的 UI 設計。

## 主要概念與 API
透過 CSS 與 HTML 的新屬性，開發者現在可以精細控制下拉選單的各個組成部分：

1.  **`appearance: base-select`**：啟動客製化模式的關鍵屬性，移除瀏覽器預設樣式，允許開發者自定義按鈕與選單外觀。
2.  **偽元素與選擇器**：
    *   `::picker-icon`：用於自定義選單右側的箭頭圖示（可透過 `content` 屬性更換符號）。
    *   `::picker-select`：用於定位並自定義彈出的下拉選單容器。
    *   `::check-mark`：用於自定義選項被選中時的勾選圖示。
    *   `:open` 偽類：用於處理選單開啟時的按鈕狀態（如顏色變化）。
3.  **`<selectedcontent>`**：一個全新的元素，允許開發者在選單按鈕內顯示當前選中項目的「豐富內容」（Rich Content），例如圖片、SVG 圖示或複雜排版，而不僅僅是純文字。

## 實作模式與技術示範

### 1. 基本樣式客製化
透過設定 `appearance: base-select`，開發者可以輕鬆調整按鈕的字型、邊框、背景與間距。
```css
select {
  appearance: base-select;
  font-family: 'Gill Sans', sans-serif;
  /* 自訂邊框與內距 */
}

select::picker-icon {
  content: "▼"; /* 或自定義 SVG glyph */
}
```

### 2. 佈局的突破 (Grid Layout)
因為該功能基於原生 HTML，選單內部現在可以結合 CSS Grid 進行複雜排版：
```css
select::picker-select {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}
```

### 3. 加入豐富內容
可以在 `<option>` 內放置任何 HTML 標籤（如 `<img>` 或 `<svg>`），並利用 `<selectedcontent>` 在選單按鈕內同步顯示：
```html
<select>
  <button>
    <selectedcontent></selectedcontent>
  </button>
  <option>
    <img src="flower.svg" alt="" />
    <span>Flowers</span>
  </option>
</select>
```

## 開發者實用重點

*   **漸進式增強 (Progressive Enhancement)**：該功能具備極佳的向後相容性。在不支援的瀏覽器中，選單會自動退回到原生的系統下拉選單，確保功能可用性。
*   **無需額外 JS**：透過純 CSS 的狀態控制（如 `:open`）與偽元素，開發者可顯著減少為了處理選單開合與樣式而編寫的 JavaScript 邏輯。
*   **無障礙友善 (A11y)**：因為底層仍是標準的 `<select>` 元素，鍵盤導航（Tab/Arrow keys）與螢幕閱讀器（Screen Readers）的支援性是開箱即用的，無需開發者手動處理複雜的 ARIA 屬性。
*   **最佳實作建議**：
    *   若在 `<option>` 內使用圖像，務必將 `<img>` 的 `alt` 屬性設為空值（`alt=""`），避免螢幕閱讀器重複讀取文字標籤。
    *   在開發過程中，務必在不支援此功能的瀏覽器上測試，並搭配輔助工具確保介面運作正常。
    *   建議參考 [WebKit.org](https://webkit.org) 上的技術部落格，獲取更多關於如何整合此功能的最佳實踐。

---
*本文整理自 WWDC 2026 Session: "Rediscover the HTML select element"*

<!-- resources -->

---

## 資源連結

- [WebKit.org - Example website demonstrating Customizable Select](https://webkit.org/demos/customizable-select/)
- [WebKit.org - CSS Grid Lanes Field Guide](https://gridlanes.webkit.org)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/315/4/f3bd9835-9ced-4f6a-a0f1-655000972674/downloads/wwdc2026-315_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/315/4/f3bd9835-9ced-4f6a-a0f1-655000972674/downloads/wwdc2026-315_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/315/
