# Discover USDKit and what’s new in OpenUSD

這是一份關於 WWDC 2026 Session「Discover USDKit and what’s new in OpenUSD」的技術摘要。

---

# WWDC 2026 技術摘要：探索 USDKit 與 OpenUSD 的最新進展

## 概述
本場次主要探討 Apple 在空間運算領域的技術基石——**OpenUSD (Universal Scene Description)**。Apple 持續推動 USD 成為業界標準，並在今年推出了強大的新框架 **USDKit**。此外，Apple 將 USD 的應用場景延伸至 macOS 的 Preview（預覽程式）、Safari 網頁瀏覽器，以及更高效的空間協作工作流。

---

## 主要概念與框架介紹

### 1. USDKit (全新框架)
USDKit 是專為 Swift 開發者設計的新系統框架，提供對 USD 的「一等公民」支援。它封裝了複雜的 USD 底層操作，讓開發者能以更直觀、符合 Swift 慣例的方式存取與操作 3D 場景。它與 RealityKit 和 Spatial Preview 深度整合。

### 2. 核心 USD 概念
*   **Layer (層)**：單一的數據檔案，作為 USD 的基本構建單位。
*   **Composition (組合)**：將多個層合併的技術，實現場景的高度模組化與參考連結。
*   **Stage (舞台)**：由多個層組合而成的最終場景，是操作 3D 內容的入口。
*   **Prim (Primitive)**：場景中的基本物件單位，具有 Schema（定義類型）與 Attributes（屬性/數據）。

### 3. 生態系工具更新
*   **macOS Preview**：現在支援 3D 編輯，包含場景操作、燈光調整與層級編輯，並內建全新的**高保真光跡追蹤渲染器 (Ray Tracer)**。
*   **Spatial Preview Framework**：支援 Mac 與 Vision Pro 之間的即時連結，可在 Mac 上編輯並於 Vision Pro 即時預覽，透過 SharePlay 實現多人協作。
*   **Web 支援**：Safari 新增 `<model>` 標籤，讓 USD 模型能像圖片一樣內嵌於網頁，在 macOS/iOS 可互動，在 visionOS 可實現空間化的 3D 呈現。

---

## 技術與程式碼實作

### 使用 USDKit 加載與操作場景
開發者可以使用 `USDStage` 輕鬆讀取檔案並透過組成 (Composition) 增加元件：

```swift
// 1. 開啟 Stage
let stage = try USDStage.open(url: sceneURL)

// 2. 定義一個新物件 (Prim)
let transformPrim = stage.defineTransform(at: "/MyObject")

// 3. 使用 Reference 連結外部檔案 (保持場景輕量化)
transformPrim.addReference(to: assetURL)

// 4. 動態調整位置
transformPrim.addTransformOperation()
transformPrim.setTranslation(x: 0, y: 1.5, z: 0)
```

### 加入無障礙資訊 (Accessibility)
Apple 推動了 USD 內部的無障礙標記標準，確保 3D 物件能被輔助技術識別：

```swift
// 套用 Accessibility API Schema
let prim = stage.getPrim(at: "/MyObject")
prim.applySchema(AccessibilitySchema.self)

// 設定標籤與描述
prim.setAttribute(name: "label", value: "Oscilloscope")
prim.setAttribute(name: "description", value: "A high-precision measurement tool...")
```

---

## 開發者實用重點

### 1. 效能優化：新一代壓縮技術
*   **Mesh Compression**：與 Alliance for Open Media 合作，引入新編解碼器，Mesh 大小最高可減少 90%。
*   **Texture Compression**：支援 AVIF 壓縮。
*   **實作方法**：透過 `exportPackage` API 並啟用 export options 即可完成。

### 2. 內容表示：Gaussian Splats (高斯噴濺)
*   引入了新的 **Particle Fields** USD 原生類型。
*   支援 Gaussian Splats，這是一種由數百萬個粒子組成的 3D 呈現方式，能極高精確度地還原真實環境光影。這讓開發者能在傳統 Mesh 場景中同時混入高斯噴濺數據。

### 3. 多種整合路徑選擇
根據開發需求，開發者有三種選擇：
*   **USDKit**：Apple 平台 App 開發的首選，系統原生、深度整合 Swift。
*   **Swift USD**：適合進階需求或需要使用開源 Swift 綁定的場景。
*   **C++ 框架**：針對跨平台架構，直接將 OpenUSD 作為 C++ 框架嵌入。

### 4. 參與標準制定
Apple 強烈建議開發者參與 **Alliance for OpenUSD (AOUSD)** 的工作小組，共同定義幾何、材質與物理屬性的行業規範，這是確保未來 3D 內容互通性的關鍵。

---
*註：相關 API 的詳細使用說明與範例，請參考官方提供的 Spatial Preview 與 USD 開發者文件資源。*