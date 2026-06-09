# Supercharge your spatial workflows with Reality Composer Pro 3

這是一份針對 **WWDC 2026: Supercharge your spatial workflows with Reality Composer Pro 3** 的詳細技術摘要。

---

# 技術摘要：利用 Reality Composer Pro 3 提升空間運算工作流程

## 概述
本場 Session 由工程師 Vincent 主講，介紹了 Reality Composer Pro 3 (RCP 3) 的重大更新。RCP 3 強調「視覺化、無程式碼（No-code）」的開發體驗，透過強大的節點編輯器，讓開發者能直接在編輯器內完成 3D 場景建構、角色動畫、互動邏輯、導航系統及 GPU 驅動的粒子特效，大幅縮短原型開發與迭代的時間。

---

## 主要概念與核心功能

RCP 3 引入了幾項關鍵的視覺化編輯器工具：

### 1. 動畫圖表 (Animation Graph)
*   **用途**：控制角色在運行時的動作邏輯。
*   **關鍵組件**：
    *   **狀態機 (State Machine)**：定義不同動畫狀態（如 Idle、Walk）及轉換條件（Transitions）。
    *   **混合空間 (Blend Spaces)**：實現動畫之間的平滑過渡。
    *   **參數控制**：透過布林值（Boolean）等參數，在運行時由外部驅動狀態切換。

### 2. 行為樹 (Behavior Tree)
*   **用途**：定義實體（Entity）的自主行為與多步驟流程。
*   **節點類型**：
    *   **組合節點 (Composite Nodes)**：如 Sequence（順序執行）、Selector（選擇執行）、Parallel（並行執行）。
    *   **動作節點 (Action Nodes)**：如 `moveTo`、`rotateToPhase`、`wait`、`parameterSetter`，用於實際執行任務。

### 3. 腳本圖表 (Script Graph)
*   **用途**：定義場景中的互動邏輯與事件驅動行為。
*   **核心特性**：事件驅動（如 `onInitialize`、`onTap`），允許開發者無需編寫 Swift 程式碼即可綁定互動與參數變更。

### 4. 導航網格 (Navigation Mesh)
*   **用途**：讓角色自動路徑規劃並避開障礙物。
*   **關鍵功能**：支援設置 `Off-Mesh Connections`（如梯子、跳躍點），讓非連續區域也能進行路徑規劃。

### 5. 計算圖表 (Compute Graph)
*   **用途**：基於 Metal 的 GPU 粒子系統。
*   **四階段生命週期**：
    1.  **Emitter**：控制粒子發射方式（連續、爆發）。
    2.  **Initialize**：設定粒子初始屬性（速度、生命週期、大小）。
    3.  **Simulate**：每幀運算（重力、湍流）。
    4.  **Output**：定義視覺外觀與著色器輸出。

### 6. Shader Graph 進階更新
*   **RealityKit PBR Surface 2**：新增 Sheen（光澤）、Subsurface Scattering（次表面散射）及更精準的遮蔽計算。
*   **Hair Surface**：專為毛髮與纖維渲染設計的專用著色器。
*   **Portal Surface**：強化對傳送門（Portal）像素級透明度與幾何變形的控制。

---

## 示範的技術模式

*   **參數驅動邏輯**：透過 `Parameter Setter` 節點連結行為樹與腳本圖表，實現「點擊物件後觸發特定 AI 流程」的互動模式。
*   **嵌套行為**：透過 Subgraph 封裝重複邏輯（如設定目標位置），使主圖表簡潔且易於維護。
*   **動態狀態追蹤**：在 Animation Graph 中即時觀察節點的高亮狀態，用於除錯複雜的角色過渡。

---

## 開發者實用重點

1.  **提升原型開發效率**：RCP 3 支援「即時預覽（Live Preview）」，開發者可以在 Apple Vision Pro 上直接測試場景互動，無需重複編譯程式碼。
2.  **與 Swift 整合**：雖然主打視覺化，但所有節點參數均可透過 Swift 程式碼進行控制，實現視覺化編排與程式碼邏輯的高度結合。
3.  **效能考量**：Compute Graph 利用 GPU 處理大量粒子，能有效處理複雜的視覺特效而不影響場景的流暢度。
4.  **學習資源建議**：
    *   若想深入瞭解互動設計，請參考 Session: *Design No-Code Games with Reality Composer Pro 3*。
    *   若想學習如何透過程式碼操作這些組件，請參考 Session: *Explore Advances in Reality Kit*。

---
*編輯建議：建議開發者先行下載 Reality Composer Pro 3，並從官方提供的「Sharp Row Village」專案下手，觀察其行為樹與導航網格的實際配置方式。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/393/4/e68b947f-f7f3-49b5-b959-7a70fd9899c3/downloads/wwdc2026-393_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/393/4/e68b947f-f7f3-49b5-b959-7a70fd9899c3/downloads/wwdc2026-393_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/393/
