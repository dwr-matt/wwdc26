# Build live production tools for Apple Immersive Video

這份技術摘要旨在協助開發者深入理解 Apple 在 WWDC 2026 發布的「Build live production tools for Apple Immersive Video」議程核心，為建構下一代沉浸式直播製作工具提供指南。

---

# 技術摘要：建構 Apple Immersive Video 直播製作工具

## 概述
本議程由 Apple Immersive Video Live Engineering 團隊主管 Jared King 主講，探討了如何針對 Apple Vision Pro 打造專業級的沉浸式直播生產管線（Live Production Pipeline）。Apple 沉浸式影像要求極高的解析度、幀率及空間音訊，這要求開發者拋棄傳統 2D 直播的舊有框架，採用基於 ProRes 的標準化工作流程，並透過 IP 網絡（SMPTE 2110）進行媒體傳輸，以確保製作過程中的零品質損耗。

---

## 主要概念與架構

### 1. 沉浸式生產流程的三大基石
*   **媒體格式標準**：使用原生 ProRes 串流作為影片核心，確保品質與效能平衡；音訊採用 ASAF（Apple Spatial Audio Format）；中繼資料則透過每幀（Per-frame）的 JSON 物件進行同步傳遞。
*   **SMPTE 2110 傳輸層**：這是直播業標準的 IP 傳輸協定。Apple 將其細分為：
    *   **2110-22**：傳輸 Apple Immersive ProRes 影片（包含左右眼數據）。
    *   **2110-30**：傳輸高解析度無損 PCM 空間音訊（ASAF）。
    *   **2110-41**：傳輸 JSON 格式的實時中繼資料（如鏡頭校正、創意事件）。
*   **零損耗儲存與重播**：直播現場的內容錄製與回放必須避免壓縮帶來的「世代損耗（Generational Loss）」。由於全程皆為 ProRes，檔案寫入時直接儲存為 `.mov`，無需額外編解碼，實現即時回放的無損品質。

---

## 關鍵 API 與框架

開發者主要透過以下 Apple 原生框架建構沉浸式生產工具：

*   **AV Foundation / AV Asset Writer**：負責將直播串流寫入 QuickTime MOV 容器的關鍵工具。
*   **Video Toolbox**：處理 ProRes 編碼與格式屬性。
*   **Audio Toolbox / Core Audio**：處理多通道、高解析度的空間音訊數據。
*   **Immersive Media Support (IMS) 框架**：**（重點！）** 專為 Apple Immersive Video 打造，用於解析鏡頭校正、相機 ID 以及寫入/讀取 Metadata Box Exchange Format (MEB Extracts) 的核心框架。

---

## 技術實作重點模式

### 1. 寫入 MOV 檔案時的設定
在寫入沉浸式影片軌道時，必須正確標記檔案格式，讓系統識別其為沉浸式內容：
```swift
// 設置 Apple Immersive Video 投影類型
let settings: [String: Any] = [
    AVVideoCompressionPropertiesKey: [
        kVTProjectionKindKey: kVTProjectionKindAppleImmersiveVideo
    ]
]
```
*這將自動添加 VEXU（Video Extended Usage）靜態中繼資料。*

### 2. 處理中繼資料（Metadata）
直播中的 JSON 中繼資料需透過 `IMS` 進行處理：
1.  **序列化/反序列化**：將 2110-41 接收到的 JSON 轉化為物件。
2.  **鏡頭校正與對齊**：使用 `IMS` 建立對應的鏡頭校正與相機 ID 物件。
3.  **同步寫入**：利用 `AV Asset Writer` 將處理後的 IMS 物件寫入 MOV 的 Metadata Box，確保其與視訊/音訊軌完全同步。

---

## 開發者實用重點

1.  **擁抱 IP 化架構**：熟悉 SMPTE 2110 標準是進入此領域的門票。雖然這對許多開發者來說是新挑戰，但其網絡化架構（Multicast RTP）是實現大規模沉浸式直播的基礎。
2.  **善用 Apple Silicon**：由於 ProRes 對 Apple Silicon 有極佳的硬體加速優化，選擇 Mac Studio 或高效能 Mac 作為製作節點（如切換台、錄影機）能獲得最佳效能。
3.  **避免重複壓縮**：在設計管線時，應時刻保持媒體內容為 ProRes 格式。任何不必要的解碼再編碼動作都會導致沉浸式體驗的嚴重劣化。
4.  **學習資源建議**：
    *   參閱 Apple ProRes 的開發者文件。
    *   造訪 **SMPTE 官網** 學習 IP 網絡的最佳實作。
    *   深入研究 **Immersive Media Support (IMS)** 框架，這是目前與沉浸式 metadata 打交道的最重要介面。
    *   觀看「Apple Immersive Video Technologies」與「Immersive Video Playback」相關議程，以理解前端與後端的技術銜接點。

**總結：** 沉浸式直播正處於起步階段，硬體與軟體管線的建構充滿機會。開發者若能結合現有的 broadcast 專業知識與 Apple 的 IMS 框架，將能在這個全新的娛樂媒體領域佔有一席之地。

<!-- resources -->

---

## 資源連結

- [kVTCompressionPropertyKey_ProjectionKind](https://developer.apple.com/documentation/VideoToolbox/kVTCompressionPropertyKey_ProjectionKind)
- [CMVideoCodecType](https://developer.apple.com/documentation/CoreMedia/CMVideoCodecType)
- [Apple ProRes RAW White Paper](https://www.apple.com/final-cut-pro/docs/Apple_ProRes_RAW.pdf)
- [Apple ProRes White Paper](https://www.apple.com/final-cut-pro/docs/Apple_ProRes.pdf)
- [Immersive Media Support](https://developer.apple.com/documentation/ImmersiveMediaSupport)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/338/5/4549be24-44c7-4214-ab9b-f21f9ed04691/downloads/wwdc2026-338_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/338/5/4549be24-44c7-4214-ab9b-f21f9ed04691/downloads/wwdc2026-338_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/338/
