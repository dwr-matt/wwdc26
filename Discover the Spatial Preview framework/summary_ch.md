# Discover the Spatial Preview framework

這是一份關於 WWDC 2026 Session「**Discover the Spatial Preview framework**」的技術摘要。

---

# 技術摘要：Discover the Spatial Preview framework

## 概述
本 Session 介紹了 Apple 新推出的 **Spatial Preview** 框架，旨在協助 macOS 開發者將應用程式內的 3D 或空間內容（Spatial Content）無縫推送到 Apple Vision Pro 上進行預覽與編輯。透過此框架，開發者可以利用 Vision OS 的強大空間運算能力，讓創作者在 Mac 上進行設計時，能即時在 Vision Pro 中以沉浸式方式檢視成果，並支援跨設備的雙向編輯與同步。

---

## 主要概念與框架介紹

Spatial Preview 框架主要分為兩大類型的預覽會話（Session）：

1.  **Document Preview Session**：
    *   適用於靜態空間媒體，如：Apple Immersive Video 影格、空間照片、PDF、標準影像檔等。
    *   當會話啟動時，Vision OS 的 Quick Look 會自動開啟並載入該內容。

2.  **USD Preview Session**：
    *   針對 3D 內容設計，底層整合了 **USD Kit**。
    *   允許在 Vision Pro 上進行高階互動，例如：查看 3D 場景、切換相機視角、材質覆寫（Material Overrides）、物件操作與註釋。
    *   支援即時同步編輯，雙方設備的操作皆會立即反映。

### 核心運作邏輯
*   **端點連接 (Endpoint)**：透過 Mac Virtual Display 偵測已連接的 Vision Pro，或使用框架提供的 `SpatialPreviewDevicePicker` UI 讓使用者手動選擇區域網路內的設備。
*   **自動優化**：對於過於複雜的 USD 模型，框架會自動進行網格精簡（Mesh Decimation）與材質採樣，確保在 Vision Pro 上具備良好的效能。開發者可透過設定 `unmodified` 參數來停用此優化，但若模型過大則可能會導致同步失敗。

---

## 程式碼模式與實作技術

### 1. 建立預覽會話 (Document Preview)
開發者僅需幾個簡單步驟即可將影像傳送到 Vision Pro：

```swift
// 1. 取得目標端點
let observer = ConnectedSpatialEndpointObserver() 

// 2. 建立文件預覽會話
let session = DocumentPreviewSession(documentType: .image, name: "LivingRoomRender")

// 3. 啟動會話並提供內容 URL
session.start(on: selectedEndpoint, url: contentURL)
```

### 2. 更新現有內容 (Update Contents)
若要在不重新開啟場景的情況下更新畫面（例如切換畫廊中的影像），應使用 `updateContents` 方法，這能確保沉浸式體驗的連續性：

```swift
// 在畫廊切換時呼叫
session.updateContents(to: newContentURL)
```

### 3. USD 預覽與編輯
USD Preview 透過 `USD Kit` 進行處理。若要讓物件在 Vision Pro 上可被移動，必須在 USD 檔案中設定 `spatial editable` metadata。

*   **雙向同步**：框架會自動同步 USD Stage 的變更。若需要監聽來自 Vision Pro 的變更（如註釋），可訂閱 `didChange` 通知：
    ```swift
    // 訂閱 USD 變更通知
    USDNotice.ObjectsDidChange.addObserver(self) { notice in
        // 解析路徑並更新 Mac 端 UI
    }
    ```

---

## 開發者實用重點

1.  **利用 USD Kit**：建議開發者優先採用 Apple 的 `USD Kit`，若既有專案使用其他 USD 庫，可透過橋接機制（Bridging）與 USD Kit 同步編輯資料。
2.  **SharePlay 支援**：此框架原生支援 SharePlay。當多位使用者在同一個直播會話中時，任何一方的編輯都會即時推送給所有參與者，極大優化了遠端協作的效率。
3.  **場景優化 (Optimization)**：雖然系統會自動優化 3D 內容，但若要維持高精細度，開發者仍需注意模型架構的渲染負擔。若遇到複雜場景建議參考官方的「減少渲染成本」文件。
4.  **UI 整合建議**：
    *   在 Mac App 中使用 SwiftUI Sheet 封裝 `SpatialPreviewDevicePicker`，提供良好的設備選取體驗。
    *   透過 `ProgressReporter` 監控同步狀態，在傳輸大型 USD 檔案時顯示進度條，提升使用者體驗。

### 總結
Spatial Preview 降低了將桌機應用程式延伸至空間運算領域的門檻。不論是開發創意工具、審閱軟體，還是 3D 建模 App，開發者只需少量程式碼即可為使用者解鎖強大的沉浸式生產力工具。

<!-- resources -->

---

## 資源連結

- [Reducing the rendering cost of RealityKit content on visionOS](https://developer.apple.com/documentation/visionOS/reducing-the-rendering-cost-of-RealityKit-content-on-visionOS)
- [Spatial Preview](https://developer.apple.com/documentation/SpatialPreview)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/282/5/958c34c9-f20e-4c6d-826a-eeed7ce7ba9e/downloads/wwdc2026-282_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/282/5/958c34c9-f20e-4c6d-826a-eeed7ce7ba9e/downloads/wwdc2026-282_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/282/
