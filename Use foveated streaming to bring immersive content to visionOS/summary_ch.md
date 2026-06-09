# Use foveated streaming to bring immersive content to visionOS

這份技術摘要旨在為開發者整理 Apple WWDC 2026 關於「Foveated Streaming（注視點串流）」的重點技術內容。

---

# 技術摘要：利用 Foveated Streaming 將沉浸式內容帶入 visionOS

## 概述
本 session 介紹了 visionOS 26.4 引入的 **Foveated Streaming（注視點串流）** 框架。這項技術專為開發者設計，旨在將外部裝置（如高效能 PC）運行的 OpenXR 應用程式，透過網路串流至 Apple Vision Pro。核心特色在於結合了 **NVIDIA CloudXR** 的高效傳輸協議，並利用 Vision Pro 的眼動追蹤（Eye Tracking）技術，僅針對使用者「注視區域」進行高畫質渲染與傳輸，大幅優化了沉浸式體驗的效能與頻寬使用。

---

## 主要概念、API 與框架

### 1. Foveated Streaming 框架
這是 visionOS 端的 API 核心，負責管理與外部端點（Endpoint）的連線、配對與狀態監控。
*   **Session-based API：** 透過 `FoveatedStreamingSession` 管理生命週期。
*   **Message Channels：** 允許 visionOS App 與 OpenXR 端點之間進行雙向資料傳輸，可用於同步 ARKit 空間資料或自定義應用邏輯。
*   **整合性：** 與 SwiftUI、ARKit 及 RealityKit 高度整合，讓串流內容能與原生 visionOS 視窗及環境無縫混搭。

### 2. NVIDIA CloudXR 整合
*   **OpenXR Runtime：** 外部端點（Windows PC）需使用 NVIDIA CloudXR SDK 提供的 OpenXR Runtime。
*   **效能優化：** 支援低延遲無線串流（Wi-Fi），自動將手部追蹤、控制器輸入及麥克風資料回傳至 PC。
*   **深度處理：** 建議提供 Alpha Channel 資訊，以便將串流內容與真實環境進行合成；若提供深度緩衝（Depth Buffer），還能實現真實物體與虛擬物件的遮擋效果（Occlusion）。

---

## 關鍵開發模式與技術

### 串流流程架構
1.  **發現與配對：** 使用 Bonjour 協議發現網路上的串流端點。
2.  **QR Code 配對：** endpoint 產生包含 `client token` 與 `hash of certificate` 的 JSON 格式 Barcode，Vision Pro 端掃描後完成安全連線。
3.  **渲染與合成：**
    *   在 visionOS 端，使用 `ImmersiveSpace` 配合 `FoveatedStreamingSession` 呈現串流內容。
    *   使用 `RealityView` 混合原生 RealityKit 物件與串流畫面。
    *   **Progressive Immersion（漸進式沉浸）：** 推薦將串流視窗以「門戶（Portal）」形式呈現，讓使用者保留對物理環境的感知。

### 訊息通道 (Message Channels) 的運作
*   **VisionOS 端：** 透過 `foveatedStreamingSession.messageChannels` 發送 opaque data blob。
*   **OpenXR 端：** 利用 CloudXR 擴充功能來接收與傳送這些資料。
*   **應用場景：** 遊戲選單狀態同步、ARKit 錨點座標傳遞、場景載入進度回報。

---

## 開發者實用重點

### 1. 快速上手資源
*   **官方範例：** Apple 在 GitHub 提供完整端對端範例（包含 Windows 端協議實現與 visionOS Receiver App）。
*   **開發時程：** 官方預估約一天內可完成基礎串流實現，一週內可完成 ARKit 與原生 UI 的深度整合。

### 2. 效能診斷 (Debugging)
*   使用 Xcode 中的 **Foveated Streaming Instrument** 工具，可即時監控：
    *   串流頻寬 (Bandwidth)
    *   姿態延遲 (Pose Latency)
    *   幀率 (Frame Rate)

### 3. 注意事項
*   **Session 狀態監控：** 當裝置進入睡眠或摘除時，系統會通知串流端點（Paused），開發者需處理連線重連機制，確保使用者重新配戴後能無縫恢復體驗。
*   **空間校準：** 務必利用 ARKit 獲取真實空間資訊（如飛機模擬器的駕駛座位置），並透過 Message Channels 同步至 PC 端，以確保虛擬與實體物件的完美對齊。

---

**結語：** Foveated Streaming 為沉浸式體驗打開了新大門，讓開發者能以高效、直覺的方式，將原本受限於 PC 的高規模擬或設計軟體，完美搬移至 Apple Vision Pro，同時兼顧 visionOS 的原生交互特性。

<!-- resources -->

---

## 資源連結

- [Analyzing the performance of a foveated streaming session](https://developer.apple.com/documentation/FoveatedStreaming/analyzing-the-performance-of-a-foveated-streaming-session)
- [Establishing foveated streaming sessions with Apple Vision Pro](https://developer.apple.com/documentation/FoveatedStreaming/establishing-foveated-streaming-sessions-with-apple-vision-pro)
- [Streaming a CloudXR application to Apple Vision Pro with foveation](https://developer.apple.com/documentation/FoveatedStreaming/streaming-a-cloudxr-application-to-apple-vision-pro-with-foveation)
- [Creating a foveated streaming client on visionOS](https://developer.apple.com/documentation/FoveatedStreaming/creating-a-foveated-streaming-client-on-visionos)
- [Foveated Streaming](https://developer.apple.com/documentation/FoveatedStreaming)
- [StreamingSession: Streaming immersive content from a CloudXR™ application to visionOS and iOS](https://github.com/apple/StreamingSession)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/286/4/fa302edd-f95a-49f4-b51c-3899d49c6dec/downloads/wwdc2026-286_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/286/4/fa302edd-f95a-49f4-b51c-3899d49c6dec/downloads/wwdc2026-286_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/286/
