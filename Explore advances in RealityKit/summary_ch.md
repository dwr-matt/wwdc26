# Explore advances in RealityKit

這是一份關於 WWDC 2026 Session「Explore advances in RealityKit」的技術摘要。本場次深入探討了 RealityKit 在光影處理、導航、物理模擬、渲染效能以及沉浸式音訊方面的重大更新。

---

# 技術摘要：探索 RealityKit 的新進展 (Explore advances in RealityKit)

## 概述
本場 Session 全面介紹了 RealityKit 的最新功能更新，旨在協助開發者於 Apple 各大平台（包含 visionOS、iOS、macOS 等）構建更高保真度、更具沉浸感的空間運算體驗。重點涵蓋了從靜態光烘焙到動態軟陰影、導航網格系統、真實感布料模擬、效能監控與優化，以及基於幾何聲學的空間音訊模型。

---

## 主要概念、API 與框架介紹

### 1. 強化光影與環境互動
*   **Lightmaps (光貼圖)：** 支援透過 RealityComposer Pro 3 的 `LightBaker` 工具生成間接光與環境光遮蔽（AO），提升場景內部的細節表現。
*   **Soft Shadows (軟陰影)：** 透過調整光源的 `lightSize` 參數，模擬真實世界中光源面積帶來的半影（penumbra）效果。
*   **Projective Textures (投影貼圖)：** 允許開發者將材質紋理投影到虛擬物件或真實世界的表面（如牆壁）。
*   **Physical Space Lighting (物理空間照明)：** 將虛擬光源擴展至真實世界。當開啟此功能時，光線能根據 Scene Understanding Mesh 與使用者周遭的實體環境互動。

### 2. 導航系統 (Navigation Mesh)
*   **資源管理：** 提供導航網格資源，支援自訂區域標籤（Flag）與跨區域連結（Off-mesh connection，例如橋樑或梯子）。
*   **導航控制器：** 透過 `NavigationComponent` 與 `NavigationController`，開發者可以計算從 A 點到 B 點的路徑，並支援異步（Async）計算以保持 UI 流暢。

### 3. 進階布料模擬 (Cloth Simulation)
*   **核心組件：**
    *   `ClothBodyComponent`：定義布料物理屬性與網格。
    *   `ClothColliderComponent`：定義與布料互動的剛體（如床、人台）。
    *   `ClothSimulationComponent`：控制全域模擬參數（重力、時間步長、求解器）。
*   **Kinematic Vertices：** 透過將頂點設定為 Kinematic，可實現「固定點」功能（如將窗簾掛在掛鉤上），使其不受模擬力影響。

### 4. 效能監控與優化
*   **LOD (Level of Detail)：** 透過 `Add by Camera Distance` 或 `Add by Screen Area` 依據相機距離或螢幕佔比切換不同細節的 mesh，降低 GPU 負載。
*   **Thermal State Monitoring：** 觀察 `Thermal State Did Change` 通知，當裝置過熱時自動降低光影品質或調整 LOD 門檻，確保體驗平穩。

### 5. 3D Gaussian Splats
*   支援高保真度的 volumetric 資料渲染。API 不限定格式，開發者需提供描述空間位置、旋轉、縮放、透明度及球面調和函數（Spherical Harmonics）的 buffer。

### 6. 沉浸式音訊 (Immersive Audio)
*   **Custom Reverb Mesh：** 基於幾何聲學，開發者可定義場景的反射模型（如木地板、石牆），系統會根據物件與使用者的相對位置動態計算迴響。
*   **Custom Materials：** 可自訂吸收係數與散射係數，模擬真實材質（如厚地毯或書架）對聲音的影響。

---

## 關鍵技術示範

### 設定軟陰影 (Spotlight)
```swift
let spotlight = spotlightEntity.components[SpotlightComponent.self]
// 設定光源半徑產生軟陰影
spotlight.shadow.lightSize = 0.7 
// 設定品質 (必須為 medium 或 high)
spotlight.shadow.quality = .medium 
spotlightEntity.components[SpotlightComponent.self] = spotlight
```

### 路徑導航 (Async Compute)
```swift
let controller = NavigationController(entity: entityWithNavComponent)
let path = await controller.computePath(to: targetPosition)
// 處理 path nodes，區分普通路徑與 off-mesh 連接 (如梯子)
for node in path {
    if node.isOffMeshConnection {
        traverseLadder()
    } else {
        moveTo(node.position)
    }
}
```

---

## 給開發者的實用重點
1.  **善用工具：** 務必搭配 **RealityComposer Pro 3** 使用，特別是 `LightBaker` 與導航網格編輯，能大幅減少手刻程式碼與調參的時間。
2.  **效能至上：** 儘管新功能強大，但在 Vision Pro 等裝置上應時刻監控 `Thermal State`，並適時調整 LOD 門檻，這是維持沉浸感的關鍵。
3.  **場景理解：** `Physical Space Lighting` 與 `RoomSense` (在 Shared Space 中) 是讓虛擬物件「融入」真實環境的強大武器，開發時應優先考慮使用者空間的幾何結構。
4.  **進階學習：** 官方已釋出多個範例專案（如 Shaparo Village 及 Museum Audio Sample），建議從這些專案中學習如何整合多種組件。

<!-- resources -->

---

## 資源連結

- [Gaussian splats on visionOS](https://developer.apple.com/documentation/visionOS/gaussian-splats-on-visionos)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/279/4/ab575725-be7d-4348-a3ae-6595ef4070c4/downloads/wwdc2026-279_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/279/4/ab575725-be7d-4348-a3ae-6595ef4070c4/downloads/wwdc2026-279_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/279/
