# Collaborate on structured 3D models in visionOS

這份技術摘要旨在協助開發者掌握 visionOS 應用程式中，關於「結構化 3D 模型協作」的核心開發技術。

---

# 技術摘要：Collaborate on Structured 3D Models in visionOS (WWDC26)

## 概述
本 Session 探討了如何在 Apple Vision Pro 上構建高互動性的空間協作體驗。重點在於如何處理複雜的裝配體（Assemblies）與多維度資料，透過 RealityKit 技術實現多人同步檢視、零件拆解、內部剖視以及自動化拆解（Auto-expansion），藉此提升在產品設計審閱、工業規劃等領域的生產力。

---

## 主要概念與框架

### 1. 結構化資產準備 (Asset Hierarchy)
*   **階層重要性**：單純的幾何物件若缺乏階層結構（Flat Hierarchy），在程式邏輯中將難以選取或獨立操作。開發者應確保匯入的 3D 模型具有「部件-整體 (Part-whole)」的巢狀結構。
*   **優勢**：良好的層級結構能讓程式精準定位（如：選取單一零件進行隔離、高亮顯示或拉出操作）。

### 2. 互動式操作 (Manipulation)
*   **核心 API**：`ManipulationComponent`
*   **動態調整機制**：透過將 `ManipulationComponent` 從模型根節點（Root）移動到子節點（Children），可實現「從整體觀察到局部操作」的轉變。
*   **協作行為**：當元件位於子節點時，每位參與者可以在 SharePlay 會議中獨立操作不同的零件，而無需干擾他人的視角。

### 3.  Clipping（剖視）機制
*   **RealityKit 新功能**：利用 `ClippingComponent` 進行模型剖面展示。
*   **運作原理**：設定軸對齊邊界框（AABB），渲染器將自動丟棄邊界框外的幾何體。
*   **狀態機設計**：分為「關閉 (Off)」、「開啟 (On)」與「編輯 (Editing)」三種狀態。在編輯模式下，透過互動平面（Interactive Planes）讓使用者直覺調整剖切範圍。

### 4. 自動化拆解 (Auto-expansion)
*   **邏輯核心**：透過計算模型中各子物件的「體積加權變異數 (Volume-weighted Variance)」，自動決定最佳的拆解軸向（X、Y 或 Z 軸）。
*   **目標**：讓系統自動計算空間，將重疊的零件沿著最能展現構造的軸向展開，提升檢視效率。

---

## 關鍵技術與實作模式

### 操縱元件的動態掛載
開發者可以透過以下邏輯開關互動狀態：
```swift
// 開啟互動：將元件從根部下移至子節點
entity.components.remove(ManipulationComponent.self)
for child in entity.children {
    child.components.set(ManipulationComponent(releaseBehavior: .stay))
    child.components.set(InputTargetComponent())
    child.components.set(CollisionComponent(shapes: [.generateBox(size: ...)]))
}
```

### 剪裁平面的數學投影
為了讓使用者直覺地透過拖曳平面來改變 clipping 範圍，核心步驟如下：
1.  **座標轉換**：將拖曳手勢向量從 `ClippingPlane` 空間轉換至 `World` 空間，再轉至 `Model` 空間。
2.  **約束投影 (Projection)**：將拖曳變化量投影至該平面的法向量（Normal），確保調整量僅在特定軸向上發生，避免不必要的偏移。
3.  **計算公式**：使用 `(DragDelta · Normal) * Normal` 來獲取正確的約束變化值，更新 `ClippingComponent` 的 `bounds`。

---

## 開發者實用重點

1.  **碰撞元件不可少**：在實現互動與手勢操作時，務必確保所有參與互動的 Entity 都掛載了 `CollisionComponent`，這是 RealityKit 事件處理的基礎。
2.  **座標系意識 (Coordinate Frames)**：處理複雜交互時，腦中需明確區分：`World`、`Model`、`ClippingControl` 與 `ClippingPlane` 四個座標系。轉換不當是造成手勢偏差的主要原因。
3.  **統計學應用**：自動化拆解模型時，不要硬編碼（Hardcode）展開軸向。透過「變異數」與「權重」計算，能讓使用者介面更符合物件本身的幾何特性，帶來「自然感」。
4.  **建議進修領域**：若要精進此類 App，建議加強**線性代數（投影、點積）**與**向量數學**的基礎，這些是處理複雜空間操作的技術門檻。
5.  **相關 WWDC 資源**：
    *   *ShareVision OS experiences with nearby people* (WWDC25)
    *   *Optimize Your 3D Assets for Spatial Computing* (WWDC24)
    *   *Better Together SwiftUI and Reality Kit* (WWDC25)
    *   *Discover the Spatial Preview Framework* (本年度相關推薦)

<!-- resources -->

---

## 資源連結

- [Manipulating models with RealityKit](https://developer.apple.com/documentation/RealityKit/manipulating-models-with-realitykit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/284/4/fa1d15b1-3f28-415a-907a-8ae1bb344494/downloads/wwdc2026-284_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/284/4/fa1d15b1-3f28-415a-907a-8ae1bb344494/downloads/wwdc2026-284_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/284/
