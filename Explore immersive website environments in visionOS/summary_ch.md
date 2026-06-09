# Explore immersive website environments in visionOS

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 中關於 visionOS 網頁沉浸式體驗的核心技術。

---

# 技術摘要：Explore immersive website environments in visionOS

## 概述
本場次介紹了如何利用全新的 **Immersive API**，將 visionOS 網頁從傳統的二維瀏覽體驗提升至立體、沉浸式的空間體驗。開發者可以透過簡單的 HTML 與 JavaScript 實作，將 3D 模型無縫整合至 Safari 瀏覽器，讓使用者能夠直接在網頁中進入一個虛擬環境（例如：劇場座位預覽、遊戲場景展示），並結合影片播放、動畫與陰影投影，打造極具互動性的空間內容。

---

## 主要概念與 API 介紹

### 1. HTML `<model>` 元素
這是呈現 3D 資產的核心標籤。
- **USDZ 格式**：支援載入 `.usdz` 檔案作為 3D 場景來源。
- **環境貼圖 (Environment Map)**：透過 360 度影像捕捉環境光，為場景中的反射與照明提供真實感。

### 2. Immersive API
該 API 遵循廣泛使用的 Fullscreen API 設計模式：
- **`requestImmersive()`**：請求模型元素進入沉浸式模式。
- **`exitImmersive()`**：退出沉浸式模式。
- **狀態檢查**：可使用 `immersive` 屬性檢測當前瀏覽器是否支援，以及目前是否處於沉浸狀態。
- **CSS 偽類**：可用於根據沉浸狀態調整網頁佈局。

### 3. 與 Fullscreen API 的區別
- **空間穿透**：Fullscreen API 會覆蓋網頁內容，而 Immersive API 則是將模型呈現於瀏覽器視窗之外的空間中，網頁本身仍可見，兩者可同時運作。

---

## 程式碼模式與實作技巧

### 基礎模型載入與互動
若要將模型從網頁視窗轉變為沉浸式體驗：
```javascript
// 檢查是否支援
if (modelElement.immersiveAvailable) {
    // 監聽按鈕點擊並請求沉浸
    button.addEventListener('click', () => {
        modelElement.requestImmersive();
    });
}

// 根據沉浸狀態調整模型變換 (Entity Transform)
modelElement.addEventListener('immersivechange', () => {
    const isImmersive = document.immersiveElement !== null;
    updateEntityTransform(isImmersive);
});
```

### 優化載入策略
為了節省效能與頻寬，若不想一開始就載入複雜模型，可先隱藏該元素：
```html
<model id="myModel" style="display: none;" src="escape_room.usdz"></model>
```
當 `display` 為 `none` 時，模型資產不會被下載與解碼，直到觸發 `requestImmersive()` 時才開始載入。

### 視訊整合 (Video Docking)
透過在 USDZ 中加入 **RealityKit Annotations**，可將影片播放器掛載到 3D 場景中的特定平面（如牆上的電視）：
1. 在 Blender 外掛中標記 TV 面板區域為 `Video Docking`。
2. 呼叫影片的 `requestFullscreen()`，Safari 會自動將影片投影至該空間位置。

---

## 開發者實用重點與效能優化

### 1. 空間座標系處理
- **Inline 模式**：原點位於元素中心，遵循 CSS 佈局。
- **Immersive 模式**：原點位於使用者的雙腳處，比例為真實世界的 1:1。需在 `immersivechange` 事件中動態重設模型變換矩陣（Transform Matrix）。

### 2. 效能優化 (Performance Best Practices)
- **頂點計數 (Vertex Count)**：移除視線不可及的區域 mesh，降低負載。
- **合併實體 (Entity Count)**：減少場景中獨立物件的數量。
- **烘焙材質 (Baking)**：將光影直接烘焙至紋理（Unlit materials），以避免執行時期的重度渲染運算。
- **USD Crush**：使用此工具壓縮 USDZ 中的材質，能大幅縮短下載時間。
- **低面數遮罩 (Low-poly proxy)**：在需要接收陰影的區域使用簡化模型，以降低陰影運算的成本。

### 3. 其他推薦資源
- **Image Controls API**：透過在 `<img>` 加入 `controls` 屬性，輕鬆實現全景圖或空間照片（Spatial Photos）的沉浸式觀看體驗。
- **設計指引**：務必觀看 *“Design Immersive Environments for Vision OS Apps and the Spatial Web”*，學習如何打造照片級真實的空間環境。

---

**建議開發者下一步**：
前往 [WebKit.org](https://webkit.org) 查看線上示範，並在 Apple Vision Pro 上進行實際體驗；若在實作中遇到問題，請提交回報至 [bugs.webkit.org](https://bugs.webkit.org)。

<!-- resources -->

---

## 資源連結

- [Download - Immersive model add-on for Blender](https://developer.apple.com/download/files/web-env-blender-plugin.zip)
- [WebKit.org - Theater Ticket Sales immersive website environment demo for Apple Vision Pro](https://webkit.org/demos/model-demos/ticket-sales.html)
- [WebKit.org - Escape Game immersive website demo for Apple Vision Pro](https://webkit.org/demos/model-demos/escape-room.html)
- [GitHub: Spatial Backdrop explainer](https://github.com/WebKit/explainers/tree/main/spatial-backdrop)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/320/4/e1844891-477b-4612-ad8d-10e55bf395ba/downloads/wwdc2026-320_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/320/4/e1844891-477b-4612-ad8d-10e55bf395ba/downloads/wwdc2026-320_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/320/
