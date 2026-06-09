# Iterate your spatial scenes faster with Reality Composer Pro 3

這是一份針對 **WWDC 2026：Iterate your spatial scenes faster with Reality Composer Pro 3** 的技術摘要。

---

# 技術摘要：使用 Reality Composer Pro 3 加速空間場景迭代

本場 Session 由 Apple 工程師 JP 主講，詳細介紹了 **Reality Composer Pro 3 (RCP 3)** 的全新功能與改進。RCP 3 已脫離 Xcode 獨立運作，不僅強化了迭代速度與協作效能，更引入了 AI 輔助與進階照明系統，旨在幫助開發者在不需頻繁切換開發環境的情況下，高效構建 VisionOS 空間體驗。

## 1. 主要概念與框架介紹

### 獨立應用程式化
Reality Composer Pro 3 不再整合於 Xcode 中，開發者可直接至 [developer.apple.com](https://developer.apple.com) 下載，並像一般 macOS 應用程式一樣啟動，這簡化了開發流程並提升了工具的獨立性。

### 核心架構：Entity 與 Component
*   **Entity (實體)**：場景中的基本物件單位，支援層級嵌套管理。
*   **Component (元件)**：透過組件化（如 Transform, Light, Physics 等）賦予實體特定屬性與功能。
*   **Prototype 與 Instancing (原型與實例化)**：這是本次的核心亮點之一。開發者可以將實體拖曳至 Project Browser 轉化為「原型 (Prototype)」，並多次實例化使用。對原型進行的修改會自動更新至所有實例，同時支援對個別實例進行「Override (覆寫)」，且覆寫內容可選擇重置或同步回原型。

### Compute Graph (計算圖)
透過節點編輯器，讓開發者無需深入編寫複雜的 GPU 著色器，即可建立粒子系統或流體模擬。此功能與模擬階段整合，允許在 Play 模式下即時調整參數。

## 2. 核心技術與新功能

### Live Preview (即時預覽)
透過連接實體 Vision Pro 裝置，開發者可在 Mac 上編輯，並在頭戴裝置中即時同步看到效果。這消除了部署的繁瑣步驟，實現「所見即所得」。

### Light Maps (光照貼圖)
針對場景中的靜態物件，RCP 3 引入了 Light Map 組件。
*   **功能**：預計算 (Bake) 間接照明、環境光遮蔽 (AO) 與最終渲染結果 (Beauty)。
*   **優勢**：大幅提升暗部區域的視覺深度與真實感，同時節省運行時的計算資源。
*   **預覽**：提供專用的 Light Map Preview 標籤頁，在完整 Baking 前即可預覽光照影響。

### Reality Composer Pro Assistant (AI 助理)
整合了生成式 AI 模型，支援：
*   直接生成 3D 物件與材質。
*   針對 RCP 3 的操作與技術問題提供解答。
*   加速從創意構思到場景實作的過程。

## 3. 開發者實用工作流程 (Workflow)

根據演講示範，高效的開發路徑如下：

1.  **導入與組織**：將 Blender 等軟體製作的 USD 檔案匯入，RCP 3 會自動將其優化為 Import Bundle。
2.  **場景建構**：利用 hierarchy 進行層級編排，並通過 `Add Component` 擴充功能。
3.  **模擬與調校**：利用 **Simulation Tab**，將其並排於場景編輯器旁。在 Play 模式下即時調整 Transform、Light 參數或 Compute Graph 數值，無需停止遊戲重新運行。
4.  **優化效能**：使用 Light Map Bake 來處理靜態場景的間接光照，確保體驗流暢。
5.  **跨裝置驗證**：開啟 **Live Preview**，在 Vision Pro 上確認最終呈現效果與空間感。

## 4. 給開發者的建議與總結

*   **學習資源**：若尚未熟悉 RCP 基礎操作，強烈建議回顧 WWDC23 的《Meet Reality Composer Pro》課程。
*   **深入進階**：若需深入研究 GPU 節點編輯器，請參考《Supercharge Your Spatial Workflows with Reality Composer Pro 3》。
*   **最佳實踐**：
    *   善用 Prototype 功能管理場景物件，能顯著減少重複勞動。
    *   在開發階段頻繁使用 Live Preview，特別是在調整材質與燈光時，這能最大程度減少開發者的猜測與視覺落差。
    *   在專案後期，務必針對靜態環境完成 Light Map Baking，以取得最佳的效能與視覺平衡。

Reality Composer Pro 3 透過更深度的工具整合與 AI 賦能，大幅降低了進入 spatial computing 的技術門檻，是 VisionOS 開發者不可或缺的利器。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/280/4/0f02d465-7874-4ac3-aac3-b1b792efecd3/downloads/wwdc2026-280_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/280/4/0f02d465-7874-4ac3-aac3-b1b792efecd3/downloads/wwdc2026-280_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/280/
