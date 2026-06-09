# Support the Center Stage front camera in your iOS app

這份技術摘要旨在協助開發者掌握 iOS 26 中針對 iPhone 17 系列（含 iPhone Air 及 Pro）推出的「Center Stage（人物居中）前置鏡頭」支援技術。

---

# WWDC 2026 技術摘要：在 iOS App 中支援 Center Stage 前置鏡頭

## 概述
本 Session 介紹了新款 iPhone 前置鏡頭的硬體變革——從傳統的 4:3 感光元件轉變為**方形感光元件（Square Image Sensor）**，並搭配 95 度超廣角鏡頭。這項硬體升級允許開發者在不旋轉手機的情況下，透過軟體彈性調整畫面比例，並實現自動變焦與自動旋轉（Auto-zoom & Auto-rotate）功能，提升自拍、錄影及視訊通話的體驗。

---

## 主要概念與 API

### 1. Dynamic Aspect Ratio (動態比例)
利用方形感光元件的特性，開發者可以在不重新建立 `AVCaptureSession` 或中斷預覽的情況下，無縫切換拍攝比例。
*   **支援比例**：3x4, 4x3, 9x16, 16x9, 1x1。
*   **限制**：需使用前置超廣角鏡頭，解析度支援 1280 到 4032。4032 解析度僅支援 3x4 與 4x3。

### 2. Smart Framing Monitor (智慧取景監測)
這是專為照片拍攝設計的 API，透過臉部與視線偵測，自動給出最佳取景建議（包含比例與縮放係數）。
*   **功能**：偵測到多人入鏡時自動縮小畫面或旋轉。
*   **觸發條件**：僅在 4032 解析度下生效。

### 3. Sensor Orientation Compensation (感光元件方位補償)
iPhone 17 系列將感光元件調整為直向安裝，與過往的橫向安裝不同。
*   **預設行為**：`AVCapturePhotoOutput` 預設會自動補償方位，將圖片轉正並更新 EXIF 資料，開發者無需更改既有的旋轉邏輯。
*   **進階設定**：可透過 `cameraSensorOrientationCompensationEnabled` 屬性關閉此行為以追求極致效能，但需手動處理圖片旋轉。

### 4. 視訊通話與錄影強化
*   **錄影限制**：若在錄影中更改 `Dynamic Aspect Ratio`，錄影會中斷（因 QuickTime 檔案軌道限制）。
*   **低延遲穩定模式 (Low Latency Stabilization)**：針對視訊通話，提供即時動態穩定功能，減少走路時的畫面晃動。

---

## 程式碼模式與實作指南

### 設定 Dynamic Aspect Ratio (切換比例)
```swift
// 1. 建立 Discovery Session 取得超廣角鏡頭
let discovery = AVCaptureDevice.DiscoverySession(deviceTypes: [.builtInUltraWideCamera], mediaType: .video, position: .front)
let device = discovery.devices.first!

// 2. 設定 Active Format 與 Dynamic Aspect Ratio
try! device.lockForConfiguration()
device.activeFormat = selectedFormat 
device.dynamicAspectRatio = .ratio4x3 
device.unlockForConfiguration()
```

### 使用 Smart Framing Monitor
```swift
// 監測建議取景
monitor.addObserver(self, forKeyPath: "recommendedFraming", options: .new, context: nil)

// 應用建議
func observeValue(forKeyPath keyPath: String?, ...) {
    let framing = monitor.recommendedFraming
    camera.dynamicAspectRatio = framing.aspectRatio
    camera.videoZoomFactor = framing.zoomFactor
}
```

---

## 開發者實用重點

1.  **效能最佳化**：若你的 App 高度依賴自定義影像處理，建議在測試環境中嘗試關閉 `sensorOrientationCompensation`，這能提升部分場景的處理效能，但必須確保處理好對應的 EXIF 方向。
2.  **錄影體驗建議**：在錄影模式下，若使用者觸發比例切換，請務必處理好 `AVCaptureMovieFileOutput` 的中斷與重啟邏輯，或透過 `AVAssetWriter` 進行平滑過渡。
3.  **Cooperative Mode**：在開發視訊應用時，設定 Center Stage 的 `ControlMode` 為 `.cooperative`，這能讓使用者透過 App 內的 UI 按鈕控制功能，而非僅能依賴系統的控制中心。
4.  **穩定性優先**：針對視訊通話，請務必啟用 `AVCaptureConnection.preferredVideoStabilizationMode = .lowLatency`，這能顯著提升使用者在移動狀態下的通話品質。
5.  **未來相容性**：此 API 需 iOS 26 以上支援。對於舊裝置，請務必先透過 `isSupported` 檢查各項 API 的可用性。

<!-- resources -->

---

## 資源連結

- [Supporting Center Stage front camera in your iOS app](https://developer.apple.com/documentation/AVFoundation/supporting-center-stage-front-camera-in-your-ios-app)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [AVFoundation](https://developer.apple.com/documentation/AVFoundation)
- [Capture setup](https://developer.apple.com/documentation/AVFoundation/capture-setup)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/341/4/fa1380a3-e2ab-4442-9302-817be212e991/downloads/wwdc2026-341_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/341/4/fa1380a3-e2ab-4442-9302-817be212e991/downloads/wwdc2026-341_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/341/
