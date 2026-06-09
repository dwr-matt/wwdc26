# Dive into lazy stacks and scrolling with SwiftUI

這是一份為開發者整理的 WWDC 2026 Session **《Dive into lazy stacks and scrolling with SwiftUI》** 技術摘要。

---

# 技術摘要：深入了解 SwiftUI 中的 Lazy Stacks 與滾動機制

本 Session 由 UI Frameworks 工程師 Renz 主講，深入剖析了 `LazyVStack` 與 `LazyHStack` 的內部運作機制、效能優化策略，以及在處理複雜滾動介面時的最佳實踐。

## 1. 核心概念與運作機制

`LazyStack` 的核心價值在於「按需加載（Lazy Loading）」。與標準的 `VStack` 不同，`LazyStack` 不會一次渲染所有子視圖，而是根據滾動區域（Visible Rect）動態添加或移除視圖。

*   **視圖預估（Estimation）：** 由於 `LazyStack` 不會加載所有視圖，因此其總高度與滾動偏移量（Content Offset）是基於已渲染視圖的平均高度進行「估算」的。這意味著：
    *   滾動位置可能會隨資料載入而微幅浮動。
    *   在處理 Orientation 變更或內容動態變化時，框架會自動進行調整以保持用戶體驗平滑。
*   **預取機制（Prefetching）：** 這是提升滾動效能的關鍵。當使用者接近頁面邊緣時，`LazyStack` 會在空閒時間自動提前計算並渲染下一個即將出現的視圖，從而避免滑動過程中的掉幀（Hitch）。

## 2. 關鍵技術與 API

*   **`onScrollVisibilityChange` / `onScrollTargetVisibilityChange`：** 建議取代傳統對 `contentOffset` 的直接觀測。透過判斷特定子視圖在螢幕上的可見百分比（例如 80%），可以更穩定地觸發 UI 變化（如顯示/隱藏「回到頂部」按鈕）。
*   **`pinnedViews`：** 透過 `LazyVStack(pinnedViews: .sectionHeaders)` 輕鬆實現標題釘選效果。
*   **`ScrollTransition`：** 可為滾動視圖添加動畫效果，但需注意：若 Transition 導致視圖「被推離」原始位置，可能造成 `LazyStack` 誤判視圖已離開螢幕而過早卸載。

## 3. 開發者最佳實踐與建議

### ❌ 應避免的模式 (Anti-Patterns)
1.  **在 View Body 中進行邏輯過濾：**
    *   不要在 `ForEach` 的 View Body 中使用 `if` 條件來隱藏項目。這會讓 `LazyStack` 無法精確計算子視圖數量，並強制框架保留過多狀態，導致效能下降。
    *   **解決方案：** 在「資料層」進行過濾（如使用 SwiftData 的 `Predicate`），確保 `LazyStack` 載入的視圖數量是確定的。
2.  **依賴 `onAppear` 進行昂貴的資源初始化：**
    *   如果 `onAppear` 內容過多，會導致預取機制的成果被浪費（因為預取無法執行 `onAppear`）。
    *   **解決方案：** 在 Model 物件的初始化階段或 ViewModel 中先行準備資料。
3.  **依賴 `onGeometryChange` 導致的 layout 反覆運算：**
    *   若視圖渲染後才計算高度並修改版面，會觸發二次重排，造成滾動體驗不穩定。
    *   **解決方案：** 改用 `Custom Layout` 或確保 UI 元素有固定的尺寸（如 `lineLimit`）。

### ✅ 建議的實作方向
*   **狀態管理：** 由於 `LazyStack` 隨時會銷毀視圖，請將重要的狀態（State）移至 Model 物件或更高層級的 View 中，不要放在生命週期會變動的子視圖內。
*   **巢狀使用：** 在 `LazyVStack` 中巢狀使用 `LazyHStack` 是合理的做法，這能有效節省記憶體並提升複雜列表的渲染效能。
*   **程式化滾動：** 使用 `ScrollPosition` 綁定來進行程式化跳轉，這對於 `LazyStack` 來說是高效的，即便目標視圖尚未載入，框架也能透過估算高度進行定位。

## 4. 給開發者的總結
`LazyStack` 的高效能建立在**「可預測性」**之上。只要保持 `LazyStack` 內的視圖結構穩定、過濾資料在數據層而非 UI 層執行，並在視圖出現前準備好資料，開發者就能在處理超長列表時，維持如同原生般絲滑的滾動體驗。

---
*想進一步學習相關技術，建議參考：*
*   *《Stacks, Grids, and Outlines in SwiftUI》*
*   *《Compose Custom Layouts with SwiftUI》*

<!-- resources -->

---

## 資源連結

- [Grouping data with lazy stack views](https://developer.apple.com/documentation/SwiftUI/Grouping-Data-with-Lazy-Stack-Views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/321/5/78830752-d07d-4d89-aeab-94405c084de9/downloads/wwdc2026-321_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/321/5/78830752-d07d-4d89-aeab-94405c084de9/downloads/wwdc2026-321_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/321/
