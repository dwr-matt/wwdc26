# What’s new in WebKit for Safari 27

# WWDC 2026 技術摘要：What’s new in WebKit for Safari 27

本屆 WWDC 的 WebKit 技術專場由 Jen Simmons 主講，重點介紹了 Safari 27 的核心更新。今年的 WebKit 開發重心從單純的「功能堆疊」轉向了**「品質提升與標準化」**，旨在解決開發者痛點、消除技術債，並提升跨瀏覽器的互通性。

---

## 1. 核心重點：品質提升與標準化
Safari 27 的開發目標不僅是加入新功能，更包含了對 WebKit 底層架構的全面檢視：
*   **兼容性修正：** 透過處理 Unicode 編碼與 `fromCharCode` 的歷史問題，改善了 Emoji 在不同網站上的顯示異常，提升了使用者輸入體驗。
*   **架構優化：** 針對使用了 20 年的 Block 與 Inline 佈局演算法進行重寫，解決長期以來的技術債。
*   **SVG 改進：** WebKit 團隊推動了 SVG2 標準的明確化，解決了如 Radial Gradient（放射狀漸層）中 `fx` 與 `fy` 屬性定義模糊的問題，並進行了超過 75 項改進。
*   **標準一致性：** 修正了 `min()`、`max()` 與 `clamp()` 在 HTML `sizes` 屬性中長期缺漏的支援，確保開發者能利用現代 CSS 功能進行響應式開發。

---

## 2. 主要功能與 API 更新

### A. CSS Grid Lanes
原生支援 Masonry（瀑布流）佈局，無需任何 JavaScript。
*   利用 CSS Grid 定義軌道，並能處理雙向排列。
*   **除錯支援：** Web Inspector 新增了順序編號（order numbers）功能，方便開發者調校頁面導覽順序。

### B. Customizable Select
`<select>` 元素的 UI 重大革新，解決了長期以來難以自訂樣式的問題。
*   透過 `appearance-based-select` 屬性，讓選單繼承頁面的 CSS 屬性（如字體、色彩）。
*   支援透過偽元素（Pseudo-elements）自訂 `picker` 彈出選單。
*   允許在 `<option>` 內嵌入 HTML 內容（如圖片、副標題）。

### C. 3D 模型元素 `<model>`
繼影像與影音之後，將 3D 模型作為原生 HTML 元素（繼承自 `<model>` 標籤）。
*   **跨平台支援：** 擴展至 iOS、iPadOS 與 macOS。
*   **互動性：** 支援環境光（Environment Map）與互動模式（Stage Mode）。
*   **AR 整合：** 使用 `<a rel="ar">` 即可輕鬆將 3D 模型引入實境空間。
*   **VisionOS 沉浸體驗：** 新增沉浸式 API，允許使用者進入模型內部空間（類似 Fullscreen API）。

### D. Safari Web Extension Packager
降低開發門檻，讓未安裝 Xcode 的開發者也能發布擴充功能。
*   現在可透過 App Store Connect 直接打包並發布至 Safari，不再受限於作業系統環境。

---

## 3. 關鍵程式碼模式與概念

### 自訂 `<select>` 範例
透過 CSS 針對 `picker` 進行控制，取代傳統僵硬的選單 UI：

```css
select {
    appearance: base-select; /* 啟用自訂 */
    /* 可繼承字體、背景等 */
}

select::picker(select) {
    /* 自訂彈出選單的樣式 */
    background-color: #f0f0f0;
}
```

### `<model>` 元素的基礎用法
```html
<model src="product.usdz" 
       environment-map="studio.hdr" 
       stage-mode="interactive">
</model>
```

---

## 4. 給開發者的實用重點

1.  **測試優先：** 建議開發者在 **Safari Technology Preview** 或 **Safari Beta** 中測試專案，這對識別兼容性問題至關重要。
2.  **善用工具：** 
    *   查看 [gridlanes.webkit.org](https://gridlanes.webkit.org/) 學習 masonry 佈局。
    *   利用 WebKit 提供的 [Safari Release Notes](https://webkit.org/) 深入了解 1,100 多項改進詳情。
3.  **參與回饋：** WebKit 團隊強調，許多修復（如 `min/max` 在 `sizes` 中的支援）源於開發者在 [bugs.webkit.org](https://bugs.webkit.org/) 提交的回報。若遇到邊界案例，請積極回報。
4.  **跨瀏覽器擴充：** 隨著 W3C Web Extensions 標準化，目前的開發方向應趨向單一程式碼庫，透過統一的 manifest 管理，減少各平台維護負擔。

**總結：** Safari 27 不僅引入了視覺上的創新（如 3D 模型與自訂下拉選單），更致力於打造一個更穩定、符合標準的網頁基礎建設，讓開發者能專注於創意而非處理瀏覽器間的差異。