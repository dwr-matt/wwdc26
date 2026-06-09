# Create high quality images using Image Playground

這是一份關於 WWDC 2026 Session「Create high quality images using Image Playground」的詳細技術摘要。

---

# 技術摘要：利用 Image Playground 打造高品質影像生成體驗

## 概述
本 session 介紹了如何將 Apple 的 **Image Playground** 框架整合至您的應用程式中。Image Playground 讓開發者能直接在 App 內調用強大的生成式 AI 模型，產生高品質、多種風格的圖片。此框架利用 Private Cloud Compute 進行處理，確保隱私性，且無需開發者維護任何後端基礎設施。

---

## 主要概念與框架介紹

### 1. 核心定位
*   **無需維護：** 無需 API Key、無需自行搭建伺服器。系統自動處理使用限制與模型互動，開發者只需專注於應用介面。
*   **私隱優先：** 透過 Apple 的 Private Cloud Compute 運行，確保數據不會儲存或與 Apple 共享。
*   **跨平台支援：** 適用於 iOS, iPadOS, macOS, 與 visionOS。
*   **棄用通知：** 原有的 `Image Creator` API 已正式廢棄，請全面遷移至新的 `Image Playground` 框架。

### 2. 關鍵組件
*   **Image Playground Sheet：** 提供完整的 UI 體驗（包含描述輸入、風格挑選、人員選擇）。
*   **Concepts (概念)：** 用於引導模型生成內容的輸入，可以是文字、草圖 (Pencil Kit)、或參考圖片。
*   **Options (選項)：** 設定生成的圖片比例、特定風格列表、以及個人化功能開關。
*   **Adaptive Image Glyphs：** 當使用 `emoji` 風格時，回傳的物件可以直接嵌入文字流中，類似 Genmoji 的效果。

---

## 程式碼模式與實現方式

### SwiftUI 整合 (推薦)
使用 View Modifier 即可快速呼叫：
```swift
// 顯示 Image Playground 介面
.imagePlaygroundSheet(isPresented: $showSheet) { url in
    // 處理生成的結果 URL
    self.imageURL = url
}
```

### 設定概念 (Context Seeding)
開發者可以預先填充上下文，減少用戶操作負擔：
*   **文字引導：** 使用 `ImagePlaygroundConcept.text("description")`
*   **圖片引導：** 直接傳入 `UIImage` 或 `SwiftUI.Image`。
*   **繪圖引導：** 結合 Pencil Kit 的 `PKDrawing`，將筆觸視為視覺參考。

### 配置與選項
透過 `ImagePlaygroundOptions` 控制行為：
```swift
ImagePlaygroundOptions(
    size: .landscape, // 或 .portrait, .square
    style: .generationStyle(default: .illustration, allowed: [.illustration, .sketch]),
    personalization: .enabled // 是否允許從照片庫選人
)
```

### 檢查可用性
在應用中檢查環境變數，以決定是否啟用功能：
```swift
@Environment(\.supportsImageGeneration) var supportsImageGeneration

if supportsImageGeneration {
    // 顯示 Image Playground
} else {
    // 顯示備選方案 (例如：傳統照片選擇器)
}
```

---

## 開發者實用重點

1.  **彈性的風格管理：**
    *   你可以透過 `allowed` 清單限制用戶能使用的風格（例如只開放「素描」與「插畫」）。
    *   支援 `ExternalProvider` (如 GPT)，若用戶在系統設定中配置了第三方服務，開啟該選項後即可在 Picker 中切換。

2.  **處理生成結果：**
    *   預設回傳的是一個 `URL`（指向 App Container 內的暫存檔），請務必在 session 結束前將檔案存至永久儲存空間。
    *   若使用 `emoji` 風格，回傳物件為 `NSAdaptiveImageGlyph`，適合與文字混合排版。

3.  **優雅降級：**
    *   不要預設所有用戶都能使用。務必透過 `@Environment(\.supportsImageGeneration)` 判斷，根據裝置效能、地區支援度與用戶設定，提供適當的 UI 備選方案。

4.  **個人化體驗：**
    *   預設開啟的個人化（從照片庫選人）是提升體驗的關鍵，若無特殊原因（如產生通用產品圖），建議保留此功能以提升與用戶的關聯感。

5.  **跨裝置一致性：**
    *   無論是 iPhone 還是 iPad，框架皆會自動處理 UI 適配。對於 iPad，可利用 Pencil Kit 結合 `ImagePlaygroundConcept.drawing`，提供更具創造力的繪圖生成體驗。

---
*提示：想了解更多關於 Private Cloud Compute 的隱私保護架構，請參考相關 session "Build with the New Apple Foundation Model on Private Cloud Compute"。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/375/
