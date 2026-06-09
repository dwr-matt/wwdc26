# Design immersive environments for visionOS apps and the spatial web

這份技術摘要旨在協助開發者掌握 Apple Vision Pro 沉浸式環境（Immersive Environments）的設計與開發流程。

---

# 技術摘要：為 visionOS 設計沉浸式環境

本 Session 由 Apple 人機介面設計師 Michael Breiman 主講，深入探討如何構建 visionOS 的沉浸式空間。這些環境不只是靜態背景，而是透過真實深度、視差（Parallax）、動態光影與空間音訊，將用戶帶入虛擬時空的互動體驗。

## 1. 核心開發流程
開發一個成功的沉浸式環境分為三個關鍵階段：

### A. 前期製作 (Pre-production)
*   **確立意圖 (Intent)：** 明確定義環境的目的（如：影音媒體體驗、演講練習空間、應用程式背景）。
*   **實地勘景 (Scouting)：** 規劃觀眾的主要視角與轉身後的效果，需考慮 VisionOS 約 81 度的沉浸式視野。
*   **參考資料：** 收集攝影、測繪數據、數位高程模型（DEM），並製作比例模型以釐清光影互動。
*   **規劃層次：** 將環境拆解為背景至前景的層次，標記需要動態效果（Motion）與空間音訊（Spatial Audio）的位置。

### B. 生產製作 (Production)
*   **優化拍攝：** 拍攝高畫質、具備動態範圍的素材。建議使用三腳架，架設 1 公尺及 2 公尺高的雙鏡頭組，以利於後期處理（補全視角）。
*   **規格建議：** 針對 VisionOS 需求，目標是 360 度全景影像，建議解析度達到 **14,400 x 7,200 像素**（符合每度 40 像素的清晰度要求）。
*   **現場紀錄：** 同時拍攝 Macbeth 色卡、灰球（Gray spheres）與鍍鉻球，以便後續 CG 資產進行完美光照匹配。

### C. 後期製作 (Post-production)
*   **清理素材：** 移除拍攝裝置、足跡等不自然元素；運用數位繪圖（Digital Matte Painting）調整構圖。
*   **技術檢查：** 進行 A/B 測試，確保 3D 網格與全景圖在紋理轉移過程中未損失品質。
*   **品質校驗：** 使用極端 Gamma 與 Gain 值檢查，確保環境在不同顯示狀態下皆無數據丟失。

---

## 2. 關鍵技術與實作模式

為了在有限的渲染預算下達到高品質的沉浸感，開發者應善用以下技術：

### 視覺優化技術
*   **UV Flow Maps：** 用於雲層流動或植物擺動，成本極低且效果自然。
*   **滾動遮罩 (Scrolling Masks)：** 模擬雲層陰影投射在地面上，而非實時渲染複雜光源。
*   **翻頁書紋理 (Flipbook Textures)：** 將複雜的陰影動畫預渲染為序列紋理，降低 GPU 負擔。
*   **分層運動 (Layered Motion)：** 透過堆疊低頻與高頻的正弦波（Sine waves）動畫，讓樹木擺動更具層次且不重複。

### 效能調校
*   **減少 Mesh 複雜度：** 對於樹葉等細節，利用 UV flow maps 處理運動，而非依賴頂點動畫。
*   **材質調控：** 水體反射可利用 Normal maps 的層疊組合，加上隨時間調變的色調（Hue/Saturation/Brightness）來模擬次表面散射，無需昂貴的即時光追。

---

## 3. 開發者實用重點 (Takeaways)

*   **空間音訊是關鍵：** 聲音能顯著提升環境真實感。務必將音源（如流水聲、風聲）精確定位於視覺動態點。
*   **「減法」藝術：** 在後期處理時，若場景過於雜亂，應大膽移除干擾視線的物件（如過於茂密的灌木），保持構圖簡潔。
*   **隨時測試：** 不要讓眼睛過度習慣單一視角。嘗試「翻轉」畫面或使用極端色彩數值來測試環境的穩固性。
*   **擁抱意外：** 開發過程中的創意修正（例如為了 Jupiter 環境而設計的太陽公轉邏輯）往往能提升產品的深度。
*   **效能監控：** 視覺效果必須在 real-time 渲染預算內完成，請務必參閱 *Optimize Your Custom Environments for visionOS* 以獲取進一步的渲染優化指導。

**總結建議：** 沉浸式環境的設計並非「一招打天下」。應先確定場景的意圖，善用預先計算的數據（如 flow maps、烘焙紋理）來減輕實時渲染負擔，並透過精確的空間音訊與動態細節，打造具備生命力的虛擬空間。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/234/4/88f2dbdd-e1b1-4b50-9fa0-69a32ac768b2/downloads/wwdc2026-234_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/234/4/88f2dbdd-e1b1-4b50-9fa0-69a32ac768b2/downloads/wwdc2026-234_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/234/
