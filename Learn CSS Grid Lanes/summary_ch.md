# Learn CSS Grid Lanes

這是一份關於 WWDC 2026 Session「Learn CSS Grid Lanes」的詳細技術摘要。

---

# 技術摘要：CSS Grid Lanes —— 原生 Masonry 佈局解決方案

## 概述
長期以來，網頁開發者若想實現類似 Pinterest 的「瀑布流」（Masonry Layout）效果，通常必須依賴 JavaScript 程式庫或複雜且容易出錯的 `float`/`flexbox` Hack。Safari 團隊在本次 Session 中正式推出了 **CSS Grid Lanes**，這是一種全新的 CSS 佈局模式，旨在彌補 Flexbox 與 Grid 之間的空白，讓內容能夠自動根據可用空間進行緊湊且交錯（staggered）的排列，同時保留內容的原始比例。

---

## 主要概念與 API

CSS Grid Lanes 的核心邏輯在於：**它將佈局維度縮減為單一軸向，而將另一個軸向留給瀏覽器進行自動計算。**

### 1. 核心定位
*   **Flexbox**：單一軸向，內容依序排列並折行，項目高度固定（或受限於容器）。
*   **CSS Grid**：雙軸向控制，項目固定在儲存格（Cell）中。若內容比例不一，容易產生留白或需要強制縮放（裁剪/變形）。
*   **Grid Lanes**：介於兩者之間。它結構化一個軸（如設定列寬），但在另一個軸（行高）上保持彈性，讓項目自動填補到最靠近頂部的空隙。

### 2. 關鍵屬性
*   `display: grid-lanes;`：啟用此佈局模式。
*   `grid-template-columns` (或 `rows`)：定義佈局的軌道數量與寬度（支援 `fr` 單位、`minmax` 等）。
*   `flow-tolerance`：這是控制佈局行為的關鍵屬性，用於微調瀏覽器決定項目放置位置的嚴格程度，避免因微小高度差異導致視覺上的混亂。

---

## 程式碼示範

### 基本佈局
只需三行程式碼即可完成瀑布流設計：

```css
.container {
  display: grid-lanes;
  grid-template-columns: repeat(3, 1fr); /* 定義三欄 */
  gap: 10px;
}
```

### 跨欄與子網格 (Subgrid)
開發者依然可以使用熟悉的 Grid 語法來控制特定項目的佔位：

```css
.featured-item {
  grid-column: span 2; /* 橫跨兩欄 */
}

.nested-card {
  display: grid-lanes;
  grid-template-columns: subgrid; /* 繼承父層軌道定義 */
}
```

### 控制流向容差 (Flow Tolerance)
`flow-tolerance` 預設為 `1em`。當兩個相鄰欄位高度差距在容差範圍內時，瀏覽器會傾向填補較高的那一欄，以維持視覺順序一致，確保 Accessibility (Tab Order) 與視覺體驗的平衡。

---

## 開發者實用重點

1.  **漸進式增強**：Grid Lanes 目前已在 Safari 26.4 實作。在其他瀏覽器中，您可以透過特性檢測（`@supports`）或 flag 來進行測試。
2.  **Web Inspector 整合**：Safari 的 Web Inspector 已完整支援 Grid Lanes。檢查時可以看到每一欄的邊界、項目的放置順序標號，以及 gap 的視覺化呈現，這對於除錯複雜的瀑布流佈局極為重要。
3.  **靈活度與限制**：
    *   Grid Lanes 不挑內容類型（圖片、文字區塊皆可）。
    *   開發者只能控制一個維度的放置（如 `column` 順序），另一個維度由瀏覽器決定，請避免在該模式下試圖強行控制所有軸向。
4.  **學習資源**：WebKit 團隊提供了《Grid Lanes Field Guide》，內含詳細的教學與互動式範例，建議開發者從中深入研究不同參數組合的行為變化。

---

## 結論
CSS Grid Lanes 是網頁佈局領域的重大進展。它將過去必須依賴外部資源處理的「瀑布流」問題，透過幾行宣告式 CSS 即可優雅解決。對於現代電商、部落格或圖片牆設計，這將顯著簡化開發流程並提升網站的渲染效能。

<!-- resources -->

---

## 資源連結

- [WebKit.org - CSS Grid Lanes Field Guide](https://gridlanes.webkit.org)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/314/4/72928edd-5728-4010-b8f0-27f1a7bdec8c/downloads/wwdc2026-314_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/314/4/72928edd-5728-4010-b8f0-27f1a7bdec8c/downloads/wwdc2026-314_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/314/
