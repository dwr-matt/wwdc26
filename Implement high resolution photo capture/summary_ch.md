# Implement high resolution photo capture

這是一份針對 WWDC 2026 Session「Implement high resolution photo capture」的技術摘要，協助開發者快速掌握高解析度攝影的實作要點與效能最佳化策略。

---

# 技術摘要：實作高解析度照片捕捉 (Implement High Resolution Photo Capture)

## 概述
本場次 session 由相機軟體工程師 Mohit Sethia 主講，旨在教導開發者如何有效率地在 iOS 應用程式中處理高解析度（12MP、24MP、48MP）照片捕捉。內容重點在於如何在**影像畫質**、**處理時間**與**應用程式反應速度**之間取得平衡，並介紹了蘋果最新的相機 API 工具，以確保在高品質捕捉的同時，應用程式依然保持流暢的體驗。

---

## 主要概念與 API

### 1. 高解析度照片格式
*   **12MP (標準)**：適合一般用途。
*   **24MP (iPhone 15+)**：透過「Photonic Engine」將 12MP 多幀融合影像與 48MP 全感測器影像結合，解析度提升但檔案大小僅增加約 50%。
*   **48MP (iPhone 14 Pro+)**：利用四像素感測器 (Quad-pixel sensor) 單幀捕捉，提供極高細節，適合裁切與放大分析。

### 2. 捕捉優先級 (Photo Quality Prioritization)
透過 `AVCapturePhotoSettings` 設定，決定處理資源的分配：
*   **Speed**：最快送出，處理最少，犧牲畫質。
*   **Balanced**：兼顧速度與畫質（預設建議）。
*   **Quality**：處理時間最長，畫質最佳（適合需要深度運算的場景）。

### 3. 關鍵 API
*   **`AVCapturePhotoOutput`**：管理相機輸出的核心類別。
*   **`setPreparePhotoSettingsArray(_:)`**：預先分配資源，避免捕捉時因資源配置導致延遲。
*   **`maxPhotoDimensions`**：指定捕捉解析度。
*   **`isResponsiveCaptureEnabled`**：允許在上一張照片處理完成前，就開始下一次捕捉（重疊捕捉）。
*   **`isFastCapturePrioritizationEnabled`**：偵測快速連續快門，動態將畫質降低為 Balanced 以維持反應速度。

---

## 程式碼模式與實作技術

### 預先分配資源 (Pre-allocation)
為了避免點擊快門時才分配記憶體造成的延遲，應在進入特定模式（如 48MP 模式）時提前通知系統：

```swift
// 1. 建立準備設定
let prepareSettings = AVCapturePhotoSettings()
prepareSettings.maxPhotoDimensions = desiredDimensions
prepareSettings.photoQualityPrioritization = .quality

// 2. 預先載入
photoOutput.setPreparePhotoSettingsArray([prepareSettings])

// 3. 實際捕捉時使用匹配的設定
let captureSettings = AVCapturePhotoSettings()
captureSettings.maxPhotoDimensions = desiredDimensions
captureSettings.photoQualityPrioritization = .quality
photoOutput.capturePhoto(with: captureSettings, delegate: delegate)
```

### 響應式捕捉流程
若要提升連續拍攝的體驗，務必啟用以下兩項屬性：
1. **Responsive Capture**：透過觀察 `isCaptureReady` 屬性，在處理階段尚未完成時提前開始下一個捕捉週期。
2. **Deferred Photo Processing**：系統會先回傳一個輕量化的 Proxy 照片，後續的高級處理移至背景進行，避免長時間阻塞 UI。

---

## 開發者實用重點

1.  **勿頻繁重組 Session**：`AVCapturePhotoOutput` 的設定一旦 Commit 後，若再更動會觸發昂貴的管線重組。應在啟動時預先設定好最大的可用解析度，再透過 `AVCapturePhotoSettings` 進行差異化捕捉。
2.  **善用 Photo Processing Time Range**：透過此屬性監控預期處理時間，在 UI 上給予用戶適當的回饋（例如載入圈圈）。
3.  **解決「快門阻塞」問題**：
    *   在處理多幀融合（如 24MP/48MP）時，務必開啟 **Deferred Processing**，這能將運算負載從前台移除。
    *   在體育賽事等需要連續拍攝的場景，請開啟 **Fast Capture Prioritization**，讓系統在連續點擊時自動降級畫質以換取速度。
4.  **記憶體管理**：高解析度照片運算非常消耗記憶體，Deferred Processing 的優勢在於將工作移到裝置閒置時進行，不僅解決了 UI 卡頓，也減少了記憶體峰值占用。

---

**總結建議**：開發高品質相機 App 時，**響應速度 (Responsiveness) 比絕對畫質更重要**。應優先採用 `Balanced` 模式與 `Deferred Processing`，僅在必要時才使用 `Quality` 模式，並利用預先配置 (`setPreparePhotoSettingsArray`) 確保用戶不會錯過任何一個重要瞬間。

<!-- resources -->

---

## 資源連結

- [Capturing photos in RAW and Apple ProRAW formats](https://developer.apple.com/documentation/AVFoundation/capturing-photos-in-raw-and-apple-proraw-formats)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/304/4/7a18d6ee-a63d-4402-bfb6-85a21dfac7dd/downloads/wwdc2026-304_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/304/4/7a18d6ee-a63d-4402-bfb6-85a21dfac7dd/downloads/wwdc2026-304_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/304/
