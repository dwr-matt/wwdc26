# Explore enhancements to visionOS object tracking

這是一份為開發者準備的 WWDC 2026 Session 摘要，主題為 **「探索 visionOS 物件追蹤增強功能 (Explore enhancements to visionOS object tracking)」**。

---

# 技術摘要：visionOS 物件追蹤與空間配件增強功能

本場次詳細介紹了 visionOS 27 在物件追蹤（Object Tracking）與空間配件（Spatial Accessories）領域的重大更新。隨著 API 的演進，開發者現在能更精確地追蹤移動中的物理物件、建立自定義的空間配件，並跨平台（iOS 與 visionOS）部署這些體驗。

## 1. 主要概念與核心功能

### 物件追蹤 (Object Tracking) 更新
*   **高幀率追蹤 (High Frame Rate Tracking)：** 提升了追蹤移動中物體的能力，對捕捉快速手持物件尤為關鍵。
*   **CreateML 擴展訓練模式 (Extended Training Mode)：** 在 CreateML 中提供新的模式，顯著提升追蹤的準確度與穩固性，建議搭配高幀率設定使用。
*   **公制空間姿態 (Pose in Metric Space)：** 新增 API 以取得物體在「公制空間」的姿態，排除顯示校正（Display Correction），解決了量測應用中座標偏移的問題。
*   **iOS 支援：** 物件追蹤能力正式引入 iOS，訓練好的 reference object 可在 iOS 與 visionOS 平台上通用。

### 空間配件 (Spatial Accessories) 進階
*   **自定義硬體開發：** 現在開放開發者自行設計空間配件。配件需具備：紅外線 LED 星座（供 visionOS 追蹤）、IMU（慣性測量單元）、以及藍牙模組（傳輸訊號）。
*   **低延遲與互動：** 透過配件內的 IMU 與 LED，實現低延遲、高頻率的追蹤，並支援透過實體按鍵與觸覺回饋（Haptics）增強互動體驗。
*   **即插即用 (Plug-and-Play)：** 將支援第三方（如 DF-robot, MicroE）提供的標準開發套件，簡化開發流程。

---

## 2. API 與實作流程

### 物件追蹤設定
開發者透過 `ARKit` 配置追蹤：
1.  **啟用高幀率：** 使用新的 `ReferenceObjectConfiguration` API，在建立 session 前針對特定物件開啟。
2.  **空間量測：** 使用 `ARKit` 的座標校正 API：
    *   `.rendered`：返回帶有顯示校正的姿態（適用於視覺對齊）。
    *   `.none`：返回原始公制空間姿態（適用於測量）。

### iOS 與 visionOS 整合
在 iOS 專案中實作流程類似：
1.  載入 `ReferenceObject`。
2.  建立 `WorldTrackingConfiguration`。
3.  於 `ARSessionDelegate` 處理 `didAdd`、`didUpdate` 與 `didRemove` 來管理物件錨點。

### 空間配件註冊流程
1.  **製作 USDZ：** 建立包含裝置 3D 模型、IMU 與 LED 位置註釋的 USDZ 檔案。
2.  **生成 Bundle：** 透過 CLI 工具生成「Reference Accessory Bundle」。
3.  **宣告類型：** 於 `Info.plist` 中將其宣告為 `exported`（製造商）或 `imported`（開發者）UTI 類型，以確保系統能辨識該硬體。

---

## 3. 開發者實用重點 (Key Takeaways)

*   **何時選擇哪種技術？**
    *   **物件追蹤：** 適合靜態或中速移動物體，若無 photorealistic 3D 模型，可透過 3D 列印標記（Marker）作為追蹤基準。
    *   **空間配件：** 適合需要「極低延遲」、「超快動作」以及「整合實體按鍵/震動回饋」的應用場景（如飛行模擬、賽車模擬）。
*   **硬體設計建議：**
    *   LED 分佈需具備獨特性，以利從不同角度識別。
    *   LED 與 IMU 必須剛性固定（Rigidly fixed）於裝置載板上，以維持追蹤精度。
    *   在手持設備設計時，需避開用戶握持區域。
*   **調試工具：**
    *   務必使用 Vision Pro 開發者設定中的 **「ARKit Accessory Tracking Debug View」**，透過紅外線攝影機驗證 LED 的亮度、IMU 的響應 latency 以及數據同步狀態。

---

**總結：** 透過這波更新，visionOS 將實體物件與虛擬世界的連結推向了新的高度。從醫療探針的精密測量到自製飛行模擬搖桿，開發者現在擁有更強大的工具箱來構建高度沈浸且實用的空間計算應用。

<!-- resources -->

---

## 資源連結

- [Working with generic spatial accessories](https://developer.apple.com/documentation/visionOS/working-with-generic-spatial-accessories)
- [Preparing spatial accessories for tracking in your visionOS app](https://developer.apple.com/documentation/ARKit/preparing-spatial-accessories-for-tracking-in-your-visionos-app)
- [Spatial accessory design guidelines for Apple devices (section 20)](https://at.apple.com/vzqbpy)
- [Exploring object tracking with ARKit](https://developer.apple.com/documentation/visionOS/exploring_object_tracking_with_arkit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/283/4/22b92960-c65b-450f-b42c-6d6bff64a9b4/downloads/wwdc2026-283_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/283/4/22b92960-c65b-450f-b42c-6d6bff64a9b4/downloads/wwdc2026-283_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/283/
