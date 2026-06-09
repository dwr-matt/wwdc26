# Build a responsive camera app that launches quickly

這是一份針對 WWDC 2026 Session「Build a responsive camera app that launches quickly」的技術摘要，旨在協助開發者優化相機 App 的啟動速度與執行效能。

---

# 技術摘要：建構啟動迅速且反應靈敏的相機 App

## 概述
相機 App 的啟動速度是用戶體驗的核心，特別是捕捉瞬息萬變的時刻（如快動作拍攝）。本 Session 由相機效能團隊的工程師 Jake 主講，重點在於如何透過「延遲初始化」技術、優化配置流程以及引入新的存儲 API，將相機預覽的顯示時間縮減一半，並確保在高效能錄影時系統的穩定性。

---

## 主要概念與 API 介紹

### 1. 相機啟動的四個階段
相機 App 啟動通常分為四個階段，優化的目標是減少每個階段的阻滯：
1. **App Launch**：載入二進位檔案、執行初始化。
2. **Session Configuration**：配置並啟動 `AVCaptureSession`。
3. **Output Initialization**：初始化 `AVCaptureOutput` 物件（此處為耗時瓶頸）。
4. **Preview Streaming**：預覽幀開始傳輸。

### 2. Deferred Start (延遲啟動 API) - iOS 26 新特性
這是優化啟動速度的關鍵。其核心理念是：**僅初始化預覽所需的資源，將其餘功能（如拍照、錄影）的初始化推遲到預覽顯示之後。**

*   **自動模式 (Automatic Mode)**：編譯連結 iOS 26 SDK 的 App 預設啟用。系統會在預覽出現後自動初始化其餘輸出。
*   **手動模式 (Manual Mode)**：適用於需要精細控制啟動時機的 App（如需讀取配置檔案或自訂 UI 後再進行初始化）。

### 3. Responsive Capture
為了在啟用「延遲啟動」時，依然能確保用戶可以快速捕捉第一張照片，Apple 引入了 `isResponsiveCaptureEnabled`。它會在後端增加緩衝機制，即使照片輸出（Photo Output）尚未完全就緒，也能回應拍攝指令。

### 4. 效能監控 API
*   **Hardware Cost API**：回傳 0 到 1 之間的值，表示當前 Session 的硬體負載。大於 1 即代表配置過重，系統無法支援。
*   **System Pressure State**：監控系統熱壓力，當狀態升高時，開發者應主動調降幀率或減少 GPU/Neural Engine 的佔用。

### 5. AVProVideoStorage (iOS 27 新增)
針對 ProRes 等高數據傳輸率的錄影，傳統檔案寫入是不確定的（Non-deterministic）。新 API 允許 App 預先分配存儲資源，確保寫入速度穩定，避免錄影過程中出現掉幀。

---

## 技術實作與程式碼模式

### 採用 Deferred Start 的基礎設定
在 `AVCaptureSession` 配置時，將非必要的輸出設定為 `isDeferredStart`：

```swift
// 設定自動模式 (iOS 26+ 預設為 true)
session.automaticallyRunsDeferredStart = true

// 僅保留預覽層，將拍照輸出設為延遲啟動
photoOutput.isDeferredStart = true
videoPreviewLayer.isDeferredStart = false 

// 註冊代理回調以處理狀態變更
session.delegate = myDelegate
```

### 處理 Deferred Start 的回調
透過代理方法監控初始化進度：
*   `sessionWillRunDeferredStart`：初始化開始前，可在這建立背景資源。
*   `sessionDidRunDeferredStart`：初始化結束，所有輸出皆已就緒。

---

## 開發者實用重點

1.  **分離 UI 初始化**：不要在主執行緒建立所有 UI。將「快門按鈕、預覽視窗」設為優先，其他輔助 UI（如相簿預覽、模式選擇器）在預覽啟動後再淡入顯示。
2.  **避免主執行緒阻塞**：`AVCaptureSession` 的建立、配置與 Start/Stop 都是阻塞型呼叫，務必移至背景執行緒處理。
3.  **預覽渲染的選擇**：
    *   **`AVCaptureVideoPreviewLayer`**：簡單、高效、自動處理 HDR 色調映射，適合一般預覽需求（此選項預設支援 Deferred Start）。
    *   **`AVCaptureVideoDataOutput`**：適合需要 per-frame 處理、整合 Metal 或自訂濾鏡的場景，但需手動實作 Deferred Start 以維持啟動效能。
4.  **測試環境差異**：效能測試不應僅限於開發者桌面環境。務必在真實的戶外場景、高溫環境下進行壓力測試，並利用 Xcode Instruments 偵測瓶頸。
5.  **針對高解析度影像**：建議觀看 WWDC 26 的 *「Implement High Resolution Photo Capture」*，學習如何將 Responsive Capture 與高品質攝影結合，確保在快速啟動的同時，不犧牲畫質。

---
*編輯建議：這項改進是相機 App 的基礎架構升級，建議優先將所有 App 重新編譯至 iOS 26+ SDK 以自動獲得 Deferred Start 的紅利，並利用新增的監控 API 提升穩定性。*

<!-- resources -->

---

## 資源連結

- [Performance and metrics](https://developer.apple.com/documentation/Xcode/performance-and-metrics)
- [AVCam: Building a camera app](https://developer.apple.com/documentation/AVFoundation/avcam-building-a-camera-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/303/5/fb6dc55a-c026-4ce1-9902-7a744fef4c99/downloads/wwdc2026-303_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/303/5/fb6dc55a-c026-4ce1-9902-7a744fef4c99/downloads/wwdc2026-303_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/303/
