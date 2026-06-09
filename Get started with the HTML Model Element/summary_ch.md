# Get started with the HTML Model Element

這份技術摘要旨在幫助開發者快速掌握 Apple 在 WWDC 2026 推出的原生 `<model>` HTML 元素，無需依賴額外的 JavaScript 庫即可在網頁中呈現 3D 內容。

---

# WWDC 2026 技術摘要：Get started with the HTML Model Element

## 概述
本場次介紹了原生 HTML `<model>` 元素，這是 Apple 繼 VisionOS 後，正式將 3D 模型渲染能力帶入 iOS、iPadOS 與 macOS Safari 瀏覽器的重要里程碑。`<model>` 元素旨在提供一種「像插入圖片一樣簡單」的方式來呈現 3D 內容，支援原生渲染、立體顯示與高效能互動，並致力於成為未來的 Web 標準。

---

## 主要概念與 API 介紹

### 1. 格式支援：USDZ
*   **首選格式**：Apple 強烈建議使用 **USDZ (Universal Scene Description)** 格式。
*   **優勢**：它能將幾何圖形、材質、貼圖與動畫封裝成單一檔案，對 Safari 的相容性與效能最佳。

### 2. 基本使用與 Fallback
`<model>` 標籤的使用方式與 `<img>` 或 `<video>` 非常相似。為了確保向後相容，建議在標籤內嵌入 `<img>` 作為備援（Fallback）。

```html
<model src="product.usdz">
  <img src="fallback.png" alt="產品 3D 模型預覽">
</model>
```

### 3. 互動與控制
*   **Stage Mode**：透過 `stage="orbit"` 屬性，瀏覽器會自動為模型開啟軌道旋轉互動，且具備自動回彈（Spring-back）功能。
*   **EntityTransform**：使用 JavaScript 的 DOM Matrix 來精確控制模型的旋轉與位置。
*   **Ready Promise**：開發者可以使用 JavaScript 監聽模型是否已準備好顯示，這對於載入中的 UI（如 Loading Spinner）處理至關重要。

### 4. AR 與沉浸式體驗
*   **AR Quick Look**：將模型包覆在具備 `rel="ar"` 的 `<a>` 標籤中，即可在 iOS/iPadOS 上觸發原生 AR 預覽體驗。
*   **空間運算**：在 VisionOS 上，該元素支援立體渲染（Stereoscopic rendering），能讓使用者將模型從頁面中「拉」出來查看。

---

## 關鍵技術與實作模式

### 處理載入狀態
```javascript
const modelElement = document.querySelector('model');

modelElement.ready.then(() => {
    // 模型已載入，移除 Loading 狀態
}).catch((err) => {
    // 處理載入錯誤，顯示 fallback
});
```

### 自定義旋轉動畫
若要手動控制模型旋轉，需先關閉 `orbit` 模式，並利用 `requestAnimationFrame` 進行平滑過渡：
```javascript
function animateTo(targetAngle) {
    // 1. 取消正在運行的動畫
    cancelAnimationFrame(animationFrameId);
    
    // 2. 設定 DOM Matrix
    const matrix = new DOMMatrix().rotateSelf(0, targetAngle, 0);
    modelElement.entityTransform = matrix;
}
```

### 控制內建動畫
如果模型檔案中已包含動畫（由 Blender/Maya 製作），可透過 JavaScript 控制播放：
```javascript
modelElement.playbackRate = 2.0; // 加速播放
modelElement.play();           // 播放
// 設定負值可倒轉播放
```

---

## 開發者實用重點與優化建議

1.  **檔案優化 (Production Optimization)**：
    *   使用 `usdcrush` 命令行工具，可在不犧牲視覺品質的情況下大幅壓縮 USDZ 檔案大小（現場範例從 7.9MB 降至 1.9MB）。
    *   使用 `usdrecord` 工具自動從模型產出縮圖或 fallback 影像，並可透過腳本批次處理整個產品庫。

2.  **Polyfill 策略**：
    *   針對尚未支援 `<model>` 的瀏覽器，可使用 W3C 建議的 Polyfill 方案，以保持未來相容性。但請注意，立體顯示（VisionOS 特有）無法透過 Polyfill 實作。

3.  **效能考量**：
    *   `<model>` 元素在虛擬空間中獨立渲染，不受頁面背景色影響。若需對齊頁面設計，務必手動設定 `background-color`。
    *   若專案允許，優先使用內建的 `orbit` 模式，因為它處理了複雜的邊界框（Bounding Box）計算與碰撞問題，比手動編寫動畫程式碼更有效率且穩定。

4.  **參與標準化**：
    *   Apple 正積極與 W3C 的「沉浸式網頁社群小組（Immersive Web Community Group）」合作。開發者若有需求或建議，可透過該組織回饋，直接影響未來的 Web 標準走向。

---

**延伸學習資源**：
*   WWDC 2024: *What’s New in USD and MaterialX*
*   WWDC 2025: *What's New for the Spatial Web*
*   Alliance for OpenUSD 官網（規格說明與轉換工具）

<!-- resources -->

---

## 資源連結

- [WebKit.org - Theater Ticket Sales immersive website environment demo for Apple Vision Pro](https://webkit.org/demos/model-demos/ticket-sales.html)
- [The HTML model element in Apple Vision Pro](https://webkit.org/blog/17118/a-step-into-the-spatial-web-the-html-model-element-in-apple-vision-pro/)
- [GitHub: model element samples](https://immersive-web.github.io/model-element-samples/)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [AOUSD – Alliance for OpenUSD](https://aousd.org)
- [w3.org – Model element](https://immersive-web.github.io/model-element)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/215/4/b7d159c9-ee29-45d9-80f5-87b6a1c90565/downloads/wwdc2026-215_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/215/4/b7d159c9-ee29-45d9-80f5-87b6a1c90565/downloads/wwdc2026-215_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/215/
