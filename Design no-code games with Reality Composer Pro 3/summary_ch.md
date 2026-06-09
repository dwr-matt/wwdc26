# Design no-code games with Reality Composer Pro 3

這是一份針對 Apple WWDC 2026 Session **「Design no-code games with Reality Composer Pro 3」** 的技術摘要。

---

# 技術摘要：使用 Reality Composer Pro 3 設計無程式碼遊戲

本場 Session 由 Apple 創意總監 Saschke Anselt 主講，重點介紹了 **Reality Composer Pro 3 (RCP 3)** 中強大的 **Script Graph** 功能。該工具讓開發者與設計師無需編寫一行程式碼，即可透過視覺化節點（Node-based）系統，在 visionOS 環境中快速原型設計（Prototyping）並構建完整的互動式體驗與遊戲邏輯。

---

## 一、 主要概念與技術框架

### 1. Script Graph (視覺化指令稿)
這是 RCP 3 的核心功能，利用事件驅動（Event-driven）架構來構建遊戲邏輯。其運作邏輯簡單明瞭：
*   **觸發 (Events)：** 監聽各類輸入，如手勢（Pinch、Drag）、碰撞、自定義觸發器。
*   **邏輯 (Logic)：** 使用各種運算節點（Math, Logic）處理數據。
*   **執行 (Set Nodes)：** 修改實體（Entity）的屬性（Component），如修改 Transform、調整 PhysicsBody 或更新 Shader 參數。

### 2. 關鍵組件 (Components)
要讓物件具備互動性，需依賴 RealityKit 的組件系統：
*   **Input Target Component：** 定義物體為「凝視」目標。
*   **Collision Component：** 定義物理碰撞範圍，與 input target 配合使用。
*   **Hover Effect Component：** 視覺化回饋，當目光聚焦時產生互動效果。
*   **Physics Body Component：** 賦予物件物理屬性（重力、阻力、質量），用於實現真實的物體移動與彈跳。

### 3. 模組化與重用性
*   **Subgraph：** 可將複雜的節點邏輯封裝成一個子圖，提升專案結構的整潔度。
*   **Prototype Subgraphs：** 將 Subgraph 轉換為原型，使其可在專案中多次重複使用。

---

## 二、 示範技術模式

### 1. 拖曳物件的物理實現
為了讓拖曳感覺更自然（而非簡單的跟隨），講者示範了物理驅動的方法：
*   **計算增量 (Delta)：** 記錄每一幀的 `targetPosition` 與之前的差值，算出 `dragDelta`。
*   **施加力量：** 利用 `addForce` 節點，根據 `dragDelta` 施加力道，使物件在拖曳過程中產生慣性與速度。
*   **動態調整 PhysicsBody：** 在拖曳開始時關閉重力（Gravity）並增加 `linearDamping`（線性阻力），讓移動過程更平穩，放下時則恢復物理模擬。

### 2. 自定義事件與節點庫 (Custom Node Library)
講者示範了如何在不同實體間進行跨節點通信：
*   **建立 Custom Node Library：** 在 RCP 3 中建立自定義事件（如 `NutIsDragged`），並為其新增屬性（如 `NutPosition`）。
*   **同步節點 (Sync Nodes)：** 點擊同步後，該自定義節點即可在專案各處呼叫。
*   **跨實體傳遞：** 透過 `sendNutIsDragged` 將 nut 的位置傳送給 Squirrel（松鼠）實體，驅動其轉動方向。

### 3. 與 Xcode 及 SwiftUI 的協作
當需要更複雜的 UI（如 SwiftUI 對話框）時，可以切換至 Xcode 模式：
*   **SendSceneEvent：** 在 Script Graph 中呼叫此節點向 Swift 端傳遞訊息。
*   **Coding Intelligence：** 透過開發工具的 AI 功能，讓 Xcode 自動偵測場景事件並觸發 SwiftUI 視圖。

---

## 三、 開發者實用重點 (Key Takeaways)

1.  **快速迭代 (Rapid Iteration)：** 利用 RCP 3 的 **Live Preview** 功能，直接在 Vision Pro 上即時測試節點邏輯，無需頻繁重新編譯專案。
2.  **變數覆寫 (Variable Overrides)：** 將變數設為「Public」後，可以在不同的 Scripting Component 實例中設定不同的預設值，大幅增加資產的靈活性。
3.  **Shader 與邏輯的整合：** 可以透過 `setMaterialParameter` 節點直接調整 Shader Graph 中的屬性，實現視覺風格（如：松鼠心情變化）與遊戲邏輯的動態掛鉤。
4.  **設計優先工作流：** 這種「設計師導向」的開發模式，將複雜的程式邏輯隱藏在直觀的節點背後，非常適合用來製作互動原型（Interactive Mockups）。

---

**建議建議：** 開發者可前往 Apple Developer 官網下載 **Squirrel 範例專案**，深入了解其 Subgraph 的封裝邏輯以及 Physics Body 的參數調校方式。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/252/6/572c2388-69f6-4e57-9eba-c71b65f5f6ed/downloads/wwdc2026-252_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/252/6/572c2388-69f6-4e57-9eba-c71b65f5f6ed/downloads/wwdc2026-252_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/252/
