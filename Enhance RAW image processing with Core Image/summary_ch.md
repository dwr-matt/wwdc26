# Enhance RAW image processing with Core Image

這是一份關於 WWDC 2026 Session「Enhance RAW image processing with Core Image」的詳細技術摘要。

---

# 技術摘要：利用 Core Image 強化 RAW 影像處理

本 Session 由 Apple 工程師 David Hayward 主講，深入探討了 Apple 平台（iOS, iPadOS, macOS, visionOS）中 RAW 影像處理的演進，重點介紹了全新的 **RAW 9** 處理管線。RAW 9 引入了基於 Core ML 的神經網路模型，顯著提升了去馬賽克（Demosaic）與降噪（Denoise）的品質，同時針對開發者提供了更高效的效能優化策略與 API 增強。

---

## 1. 核心概念與技術背景

### RAW 處理流程
Core Image 處理 RAW 檔案的核心流程包含：
1. **解析與拆包**：從 metadata 中提取原始感測器數值（Mosaic 模式）。
2. **去馬賽克 (Demosaic)**：將像素重建為完整的 RGB 數值。
3. **降噪 (Denoise)**：移除光子、讀取與熱雜訊。
4. **影像增強**：應用卷積運算進行銳化與邊緣對比度調整。
5. **最終調整**：白平衡、曝光、色調等參數優化。

### RAW 9 的技術突破
RAW 9 是目前最強大的處理管線，其特點包括：
* **Core ML 模型集成**：結合去馬賽克與降噪，實現最佳影像細節保留。
* **硬體加速**：利用 Apple Neural Engine（ANE）進行裝置端運算，確保高效能。
* **畫質提升**：在高 ISO 雜訊與非傳統感測器排列（如 Fujifilm X-T5）的處理上，細節與色彩表現遠優於 RAW 8。

---

## 2. API 與開發者應用

### 啟用 RAW 9
RAW 9 並非預設啟用，開發者需在 `CIRAWFilter` 中進行檢查與設定：

```swift
// 1. 檢查是否支援 RAW 9
if let supportedVersions = rawFilter.supportedDecoderVersions,
   supportedVersions.contains(9) {
    
    // 2. 選擇使用 RAW 9
    rawFilter.decoderVersion = 9
}

// 查詢支援 RAW 9 的相機型號
let supportedModels = CIRAWFilter.supportedCameraModels(forVersion: 9)
```

### 關鍵調整屬性變更
由於 RAW 9 透過 Core ML 自動處理色彩降噪，部分舊屬性在 RAW 9 中已失效或不再建議使用：
* **自動化處理**：色彩雜訊抑制（Color noise reduction）由模型自動完成。
* **不再支援**：`detailAmount` 與 `moiréNoiseReductionAmount` 屬性。
* **推薦使用**：`exposure`, `lumaNoiseReductionAmount`, `sharpnessAmount`, `contrastAmount`。

---

## 3. 效能優化最佳實踐

RAW 9 運算資源需求較高，Apple 建議依場景採取不同優化策略：

### 場景 A：互動式編輯 (Interactive Editing)
* **降低解析度**：使用 `CIImage` 的 `scaleFactor` 屬性，在螢幕顯示時避免處理完整像素數據。
* **快取管理**：每個 View 使用一個 `CIContext`，並將 `cacheIntermediates` 設為 `true`。
* **記憶體配置**：建議啟用 `extendedVirtualAddressing` 以增加快取空間。
* **渲染目標**：直接渲染至 Metal 視圖（MTKView），利用 Metal 的並行處理能力。

### 場景 B：檔案匯出 (Exporting)
* **快取關閉**：將 `CIContext` 的 `cacheIntermediates` 設為 `false`。
* **記憶體限制**：可透過 `contextMemoryLimit` 提升上限（從預設的 256MB 提升至 512MB 或 1024MB），顯著提升匯出效率。
* **高效格式轉換**：優先使用 `heifRepresentation` 或 `jpegRepresentation` 方法，而非手動呼叫 `ImageIO`。

---

## 4. CIImageProcessor API 的進階功能

為了提升處理複雜演算法（如 Core ML 混合 Core Image Kernel）的效率，API 進行了以下增強：

### 顯式輸出切片 (Explicit Output Tile Sizes)
開發者現在可以手動定義處理的 Tile 大小，以適應記憶體限制，避免系統對整圖進行重繪：
```swift
// 將影像拆分為 512x512 的區塊
let tiles = calculateTiles(extent: image.extent, size: CGSize(width: 512, height: 512))
image.apply(processor, with: tiles)
```

### 臨時緩衝區管理 (Temporary Buffers)
針對需要多次中繼運算的處理器，`CIImageProcessorOutput` 新增了 `temporaryBuffer` 支援，讓開發者能申請暫存空間：
* 系統自動處理緩衝區的生命週期與重用。
* 透過 identifier 管理多個暫存 Buffer。
* 減少頻繁創建與銷毀處理器的效能損耗。

---

## 開發者實用重點總結
1. **儘早遷移**：檢查應用中是否已啟用 RAW 9，這對於攝影類 App 是畫質提升的關鍵。
2. **屬性清理**：針對 RAW 9 的應用，移除不再有效的屬性設定，避免邏輯錯誤。
3. **優化快取**：區分「互動預覽」與「批次匯出」兩種快取策略，是保持 App 流暢度的核心。
4. **精細控制**：對於處理大型 RAW 檔的需求，利用新的 Tile 與 Temporary Buffer API 來解決記憶體瓶頸。

<!-- resources -->

---

## 資源連結

- [Extended Virtual Addressing Entitlement](https://developer.apple.com/documentation/BundleResources/Entitlements/com.apple.developer.kernel.extended-virtual-addressing)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/305/5/d8d5f3ce-0ff1-45a3-a630-436743477c62/downloads/wwdc2026-305_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/305/5/d8d5f3ce-0ff1-45a3-a630-436743477c62/downloads/wwdc2026-305_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/305/
