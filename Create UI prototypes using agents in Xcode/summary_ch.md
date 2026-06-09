# Create UI prototypes using agents in Xcode

這是一份針對 WWDC 2026 Session「Create UI prototypes using agents in Xcode」的詳細技術摘要。

---

# 技術摘要：使用 Xcode 中的 Agents 進行 UI 原型設計

## 概述
本課程介紹了如何利用 Xcode 中的「編碼代理（Coding Agents）」與「SwiftUI Previews」來加速應用程式的原型設計流程。Apple 設計團隊強調，開發者不應將 AI 視為設計師，而應將其視為「協作者」。透過結構化的提示（Prompting）策略、SwiftUI 的彈性，以及自定義「調試面板（Tuning Panels）」的開發技巧，開發者可以從無到有，快速迭代出具有意圖性（Purposeful）且高品質的 UI 交互體驗。

---

## 主要概念與技術亮點

### 1. 編碼代理（Coding Agents）的協作角色
*   **定位**：代理產生的是真實、原生的 Swift 代碼，這意味著原型代碼可直接轉化為正式產品開發。
*   **角色界線**：AI 負責執行繁瑣的代碼編寫，開發者負責「審核」與「決策」。開發者需運用專業判斷力，確保最終體驗符合設計意圖。

### 2. Xcode Previews 的運用
*   **即時視覺化**：透過 Show Canvas 按鈕即可即時預覽 UI 變更，無需重複 Build 與 Run。
*   **多變體並存**：善用多個 `Preview` 區塊，將不同的設計選項（例如：不同的排版、色彩方案）各自命名，便於快速切換對比。

### 3. 進階原型技巧：調試面板（Tuning Panels）
*   針對動畫參數（如 Spring 的 stiffness、damping、mass）或狀態切換，直接修改代碼常數效率較低。
*   **技巧**：請 Agent 撰寫一個專用的「調試面板 UI」，將參數控制項（Slider, Toggle, Picker）與目標 UI 並排顯示，實現不需切換 Context 的即時調整。

---

## 關鍵實作流程與程式碼模式

### 提升 Prompt 的精準度
為了避免 Agent 生成過於發散或通用的 UI，提示詞應包含：
1.  **具體功能需求**：明確定義應用程式解決的問題。
2.  **風格指引**：描述預期的情緒（如「溫暖的咖啡廳氛圍」、「清爽的排版」）。
3.  **多樣性要求**：強制要求 Agent 生成多種變體，並為每個變體創建獨立的 `Swift Preview`。

### 處理邊緣案例（Edge Cases）與內容模擬
當 App 進入「模擬真實使用（Lived in）」狀態時，應要求 Agent：
*   **邊緣狀態檢查**：如列表為空、文字過長（截斷 vs 多行顯示）、成員過多（滾動效能）。
*   **動態內容**：確保範例資料與 App 主題相關，並將模型設計為可重用（Reusable），方便後續替換。

### 動畫調試的階段化管理
將複雜動畫拆解為「階段（Phases）」：
*   **Phase 1**：頁面轉場（Transition）。
*   **Phase 2**：元件進入時的錯開動畫（Staggered timing）。
*   **開發模式**：利用 `Sidebar` 或 `Resize` 控制功能，讓調試面板不會遮擋主視圖。

---

## 開發者實用重點總結

*   **廣度（Go Wide）→ 混合（Remix）→ 重複（Repeat）**：
    *   初期先要求大量不同的 UI 原型。
    *   挑選各版本中喜愛的元件進行「混搭」。
    *   透過持續的 Iteration 修正冗餘並精簡佈局。

*   **不要被 AI 的初始輸出定錨**：
    *   如果 Agent 提供的初始導航結構不合理，應在後續 Prompt 明確指出，而非直接在該基礎上強行修改。

*   **善用參數化 UI**：
    *   在開發過程中，為重要的動畫或互動參數建立專屬 UI 控制板，能大幅縮短調整細節（如彈性係數、延遲時間）的 feedback loop。

*   **最終權限**：
    *   永遠不要將「批判性思考」委派給工具。AI 提供的僅是可能性，最終的體驗設計由開發者決定。

---

**參考資源**：如需深入了解相關技術，請搜尋並觀看 WWDC 2026 影片：*「Xcode, Agents, and you」*。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/227/4/f96c1da6-a49b-4d9a-8612-340d198d201b/downloads/wwdc2026-227_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/227/4/f96c1da6-a49b-4d9a-8612-340d198d201b/downloads/wwdc2026-227_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/227/
