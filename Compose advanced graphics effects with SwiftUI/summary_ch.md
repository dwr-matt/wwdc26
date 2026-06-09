# Compose advanced graphics effects with SwiftUI

這份技術摘要旨在協助開發者深入理解 WWDC 2026 Session「Compose advanced graphics effects with SwiftUI」的核心技術。本場演講強調「創意管線（Creative Pipeline）」的概念，即透過串接 SwiftUI 的各項基礎 API 來打造複雜且細緻的圖形與互動效果。

---

### 1. 概述：以管線思維建構進階 UI
本 Session 的核心觀點在於：**「進階」並非意指「複雜」，而是「建構方式的藝術」。** 講者透過一個 Podcast 應用程式的轉型為例，展示了如何將 UI 拆解為數據流的處理管線（Pipeline）。透過組合 Shader（著色器）、時間軸控制與對齊指南（Alignment Guides），開發者可以將原本單調的介面提升為具備現代感、流暢動畫與動態視覺反饋的高級體驗。

---

### 2. 主要概念與 API 介紹

#### A. Shader Effects (Metal Shaders)
SwiftUI 提供了強大的 `ShaderEffect` API，允許開發者直接在 GPU 上執行 Metal 程式碼。演講中區分了三種主要的 Shader 類型：
*   **Color Effect**: 針對每個像素進行顏色轉換。適用於黑白濾鏡、色調調整等簡單操作。
*   **Distortion Effect**: 改變像素的取樣位置，但不更動原始像素顏色。適用於幾何變形（如扭曲、位移）。
*   **Layer Effect**: 最靈活的類型，允許存取整個視圖層（View Layer），並透過取樣相鄰像素來達成模糊（Blur）或複雜的圖像混合效果。

#### B. TimelineView 與動態視覺
由於 SwiftUI 的 Modifier 動畫是基於 Transaction 的，但 Shader 是無狀態（Stateless）的，因此需要透過 `TimelineView` 來驅動。
*   **機制**: `TimelineView` 每一幀都會觸發並提供當下的 `Timestamp`，將此數值傳遞進 Shader，即可實現與播放狀態同步的動態背景效果。

#### C. Alignment Guides (對齊指南)
演講詳細介紹了如何精準控制視圖佈局，而非使用傳統的 Offset 偏移。
*   **原理**: 透過覆寫 `alignmentGuide`，開發者可以調整視圖的「錨點」，讓系統在計算佈局時，將特定的邊緣（如 Subview 的頂端）對齊到容器的特定位置（如容器的底部）。這種方式語意清晰，且能自動響應容器尺寸變更。

---

### 3. 實作技術與程式碼模式

#### 領域扭曲（Domain Warping）技術
為了達成有機、流動的背景效果，講者展示了「Domain Warping」技術：
1.  **Noise Texture**: 準備一張平滑隨機值的噪點貼圖。
2.  **UV 取樣**: 在 Shader 中根據像素位置計算 UV 座標。
3.  **雙重取樣**: 第一次取樣噪點取得偏移量，第二次以偏移後的座標再次取樣噪點，兩次疊加後產生的扭曲效果會呈現如流體般的「Blob」效果。
4.  **Metal 代碼模式**:
    ```metal
    // 概念代碼片段
    float2 noise1 = noiseTexture.sample(sampler, uv).rg;
    float2 noise2 = noiseTexture.sample(sampler, uv + noise1 * scale).rg;
    // 使用 noise2 進行最終偏移計算
    ```

#### 時間同步滾動（Time-Synced Scrolling）
透過 `onChange` 修飾符監控播放狀態，並結合 `ScrollViewReader` 或自動滾動機制，將當前正在播放的轉錄文字（Transcript）居中顯示。透過輔助的 `overlay` 顯示浮動時間戳，並利用 `Alignment Guides` 將其固定在特定視圖邊緣，維持版面的簡潔。

---

### 4. 開發者的實用重點

1.  **管線化設計**: 不要將複雜效果視為一個巨大的功能，而是將數據（如時間、播放狀態、視圖尺寸）視為輸入，透過一層層的 Modifier 轉換，最終產出 UI。
2.  **Shader 效能優化**: 由於 Shader 是逐像素運算，請善用預先計算好的貼圖（如 Noise Texture）而非在 Shader 內執行複雜的隨機數生成，以確保流暢度。
3.  **語意化佈局**: 盡量少用硬編碼的 `offset` 值。當需要實現複雜的浮動效果時，優先使用 `Alignment Guides`，這樣在不同螢幕尺寸或動態字體大小下，介面仍能保持一致的對齊邏輯。
4.  **實驗精神**: 講者鼓勵開發者下載範例專案，並嘗試調整參數（如噪點強度、流動速度）。即便是一個微小的 Shader 效果，也足以讓 App 的體驗在競品中脫穎而出。

**總結建議：**
開發者應將「圖形運算」與「SwiftUI 佈局」視為兩個可協同工作的領域。透過將 Metal 的強大算力與 SwiftUI 的宣告式佈局 API 結合，開發者無需深入底層框架，即可建構出具備 Apple 高品質水準的視覺效果。

<!-- resources -->

---

## 資源連結

- [Alignment](https://developer.apple.com/documentation/SwiftUI/Alignment)
- [Composing advanced graphics effects with SwiftUI](https://developer.apple.com/documentation/SwiftUI/Composing-advanced-graphics-effects-with-SwiftUI)
- [Shader](https://developer.apple.com/documentation/SwiftUI/Shader)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/322/4/db4c622a-2091-45ef-a024-df317a5b55a5/downloads/wwdc2026-322_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/322/4/db4c622a-2091-45ef-a024-df317a5b55a5/downloads/wwdc2026-322_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/322/
