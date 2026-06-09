# What's new in image understanding

這是一份為開發者整理的 WWDC 2026 **"What's new in image understanding"** 技術摘要。

---

# 技術摘要：What's new in image understanding

## 概述
本場次介紹了 Apple 在影像理解（Image Understanding）領域的重大進展，核心重點在於 **Vision Framework** 的增強與 **Foundation Models Framework** 的深度整合。開發者現在可以透過互動式的「點擊分割（Tap to Segment）」功能精確地處理物件，並結合大型語言模型（LLM）與傳統電腦視覺 API，建構更具智慧且靈活的影像分析應用。此外，Vision Framework 現已正式支援 **watchOS**。

---

## 主要概念與框架

### 1. Vision Framework: Tap to Segment API
此功能允許使用者在圖片中透過互動方式選取物件。
*   **互動模式**：支援點擊（Point）、框選（Bounding Box）、套索（Lasso）及塗鴉（Scribble）。
*   **動態調整**：支援在現有遮罩上進行「新增（Add）」或「移除（Subtract）」操作，以達到精確的分割效果。
*   **注意事項**：
    *   使用前須確保已下載模型資產（使用 `Download Assets API` 與 `Asset Status`）。
    *   座標系統為標準化的左下角原點（0.0 到 1.0）。
    *   若使用 Lasso，線條寬度至少應為圖片寬度的 1%。

### 2. Foundation Models Framework: 影像理解整合
LLM 現在支援圖片作為輸入，開發者可以將圖片作為 Prompt 的附件（Attachment），交由模型進行文字描述、分析或內容生成。
*   **優勢比較**：
    *   **Foundation Models**：具高度通用性，適合處理複雜、開放式的任務（如：根據冰箱照片生成食譜）。
    *   **Vision Framework**：處理速度快、專精於特定計算任務（如：即時影片影格分析）。

### 3. Tool Calling：橋接視覺與語言
透過 Tool Calling 機制，開發者可以讓 LLM 在遇到無法處理的特定影像任務時，自動調用 Vision 的傳統 API（如 QR Code 掃描、OCR），將結果回傳給模型。

---

## 技術與程式碼模式

### 使用 Tap to Segment
建立一個 `GenerateIterativeSegmentationRequest` 並透過 `ImageRequestHandler` 執行：

```swift
// 建立分割請求
let request = GenerateIterativeSegmentationRequest(seedPoint: point)
let handler = ImageRequestHandler(image: uiImage)

// 執行請求取得遮罩 (PixelBuffer)
try? handler.perform([request])
let mask = request.results?.first?.pixelBuffer
```

### 使用 Foundation Models 進行圖片分析
透過 Prompt Builder 將圖片作為附件傳遞：

```swift
let prompt = PromptBuilder()
    .text("描述這張圖片的內容")
    .attachment(image) // 將圖片作為輸入
    .build()

let response = try await model.generate(from: prompt)
```

### 自訂視覺工具 (Tool Calling)
若要讓 LLM 具備特定功能，需實作 `Tool` protocol：

```swift
struct PlantIdentifierTool: Tool {
    func call(arguments: [String: Any]) async throws -> String {
        // 1. 從 history session 獲取 transcript
        // 2. 解析 image reference 並轉為 pixel buffer
        // 3. 使用 Vision 執行識別並回傳結果
    }
}
```

---

## 開發者實用重點

1.  **效能考量**：在進行分割請求前，請務必處理模型下載與狀態檢查，避免阻塞主執行緒。
2.  **watchOS 開發**：由於手錶螢幕空間有限，強烈建議利用 Vision 的 **Saliency Analysis（顯著性分析）**，自動辨識圖片中的重點區域並進行裁切（Crop），以優化使用者在小螢幕上的瀏覽體驗。
3.  **混合應用建議**：
    *   **簡單任務**：優先使用 Vision API 以換取極致的效能。
    *   **複雜語意任務**：使用 Foundation Models。
    *   **瓶頸任務**：當 LLM 無法準確讀取文字或條碼時，務必引入 `BarcodeReader` 或 `OCR` 工具。
4.  **標記影像**：在使用工具調用時，記得給予附加的圖片適當的「標籤（Label）」，以利模型在複雜的對話情境中正確對應影像資源。

---
*建議觀看連結：*
*   *Discover Swift enhancements in the Vision Framework*
*   *Deep Dive into the Foundation Models Framework*
*   *What's New in the Foundation Models Framework*

<!-- resources -->

---

## 資源連結

- [Segmenting objects using taps, scribbles or rectangles](https://developer.apple.com/documentation/Vision/segmenting-objects-using-taps-scribbles-or-rectangles)
- [Implementing saliency-based image cropping in iOS and watchOS](https://developer.apple.com/documentation/Vision/implementing-saliency-based-image-cropping-in-iOS-and-watchOS)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/237/6/a3bdea1e-5c1d-44bc-8c21-9e1958774bd3/downloads/wwdc2026-237_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/237/6/a3bdea1e-5c1d-44bc-8c21-9e1958774bd3/downloads/wwdc2026-237_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/237/
