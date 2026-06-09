# Build next-generation experiences with visionOS 27

這份技術摘要整理自 WWDC 2026 Session「Build next-generation experiences with visionOS 27」，旨在協助開發者快速掌握 visionOS 27 的關鍵更新與開發工具升級。

---

# Session 技術摘要：Build next-generation experiences with visionOS 27

## 1. 概述
本場次介紹了 visionOS 27 的核心升級，重點在於如何透過更強大的渲染引擎、AI 輔助開發工具，以及全新的串流與硬體整合方案，打造更具沉浸感的空間運算體驗。visionOS 27 不僅強化了原生開發的路徑，也提供了從 macOS 和 PC 移植內容的強大銜接方案，讓不同背景的開發者能更輕易地將 2D 或 3D 內容轉化為空間體驗。

---

## 2. 主要概念、API 與框架介紹

### A. RealityKit 與 Reality Composer Pro (RCP) 3
*   **物理空間照明 (Physical Space Lighting)：** 透過新的 Projective Texture API，讓虛擬光源能精確地投影並適應現實物體表面，提升光影真實度。
*   **Cloth Simulation：** 內建高效能布料模擬系統，支援實時計算衣物摺疊、重力與垂墜效果。
*   **空間音訊 (Spatial Audio)：** 引入 **Custom Reverb Mesh**，讓聲音能根據現實空間材質（如木頭、金屬、石頭）產生真實的殘響反射。
*   **Gaussian Splatting：** 支援將現實物件掃描並以高擬真格式導入體驗，無需複雜的手動建模。
*   **RCP 3 新功能：**
    *   **Assistant (AI)：** 可根據指令生成 3D 模型與材質。
    *   **Animation Graph：** 視覺化狀態機，管理角色動作轉換。
    *   **Navigation Meshes：** 自動生成導航網格，快速設計 AI 行走邏輯。
    *   **ScriptGraph：** 節點式開發環境，無需寫程式即可串接互動邏輯。
    *   **Shader Graph 升級：** 支援 Subsurface Scattering (次表面散射)，實現更真實的皮膚、眼睛與毛髮渲染。

### B. Spatial Preview Framework (macOS)
*   讓 Mac 開發者無須重寫 app，即可將 3D 內容直接「投影」到 Vision Pro 進行預覽與協作。
*   支援透過 SharePlay 與他人進行實時 3D 內容審核、材質調整與註釋。

### C. Full Video Streaming (PC/Cloud 串流)
*   **NVIDIA CloudXR 整合：** 針對 PC 高運算負載（如飛行模擬、工業設計）的內容，提供低延遲 Wi-Fi 串流。
*   **動態頻寬優化：** 根據使用者的視線焦點（Foveation）動態調整畫質，中央區域高解析度，邊緣區域降低頻寬需求。

---

## 3. 技術實作與應用模式

### 空間輔助配件 (Spatial Accessories)
visionOS 27 開放開發者打造自訂配件，硬體需包含：
1.  **LED constellation：** 供 Vision Pro 追蹤。
2.  **IMU：** 捕捉旋轉與加速度。
3.  **Bluetooth：** 傳輸訊號。
*   **開發應用：** 使用 Game Controller Framework 進行連接，透過 ARKit/RealityKit 獲取精確的空間位姿，實現觸覺回饋與實體按鍵互動。

### Immersive Media Pipeline (AIV)
*   **Immersive Preview Renderer：** 實時預覽影片渲染效果。
*   **Wide Aspect Ratio Portals：** 在沉浸模式與視窗模式切換時，支援更寬廣的影視比例。
*   **Static Foveation：** 針對 Immersive Video 預先編碼優化，解決高解析度影片串流頻寬瓶頸。

---

## 4. 開發者實用重點 (Key Takeaways)

1.  **開發路徑選擇：**
    *   **輕鬆遷移：** 原有 iOS/iPadOS 應用透過 Xcode 設定 target 即可移植。
    *   **原生開發：** 使用 SwiftUI + RealityKit + RCP 3 獲取最深層的系統整合。
    *   **引擎支援：** Unity、Unreal、Godot 皆已支援 visionOS，建議下載專用 Plugin（GitHub 提供）。
    *   **跨平台串流：** 若應用極度吃效能（如 PC 渲染），選擇 CloudXR 串流方案。

2.  **效能最佳化：**
    *   利用 **Static Foveation** 處理高解析度影像，能有效平衡視覺品質與網路傳輸效能。
    *   使用 **Object Tracking API** 時，透過 CreateML 訓練參考物件，現已支援 iOS 與 visionOS 跨平台通用。

3.  **生產力提升：**
    *   RCP 3 的 ScriptGraph 與 AI Assistant 大幅降低了「建模→編碼→測試」的迭代時間，特別適合原型設計。
    *   新版控制中心已整合環境與狀態管理，開發者應確保 app 的空間行為能與系統 UI 和諧共存。

4.  **學習資源：**
    *   務必關注 **「Explore Advances in RealityKit」** 與 **「Discover Spatial Preview Framework」** 等專場課程以取得深層教學。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/287/4/979d9278-8250-46f9-ac82-79669ba7b479/downloads/wwdc2026-287_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/287/4/979d9278-8250-46f9-ac82-79669ba7b479/downloads/wwdc2026-287_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/287/
