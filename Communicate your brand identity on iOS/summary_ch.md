# Communicate your brand identity on iOS

這份技術摘要旨在協助開發者掌握如何在 iOS 平台上，於「品牌辨識度」與「原生操作體驗」之間取得完美平衡。

---

# WWDC 2026 Session 摘要：Communicate your brand identity on iOS

## 1. 概述
本場次主要探討如何在 iOS 應用程式中植入品牌識別（Branding）。核心主旨在於：**品牌不應是為了追求與其他平台一致而強行複製，而應在尊重 iOS 原生設計典範（Paradigms）的前提下，透過細節營造獨特性。** Apple 強調，使用者對 iOS 有既定的操作預期，開發者應將「UI 層」留給系統原生導航，「內容層」則作為品牌發揮的畫布。

---

## 2. 主要概念與設計哲學

### 應用程式的兩層模型 (The Two-Layer Model)
開發者應將 App 視為兩個不同功能的層級：
*   **UI 層 (UI Layer)：** 負責全局導航（Tab Bars、Toolbars），應維持 iOS 的原生互動模式，讓使用者無需重新學習操作邏輯。
*   **內容層 (Content Layer)：** 這是品牌展現的空間。包含視覺影像、獨特的動效、以及特定的資料呈現方式。

### 液態玻璃 (Liquid Glass) 與 iOS 26 新設計語言
iOS 26 導入了「液態玻璃」設計，建議開發者：
*   將色彩從靜態的工具列（Toolbars）釋放，融入 Scroll View 中的內容層。
*   系統控制元件會自動浮動於內容層上方，並動態採集背景色。

### 品牌體驗的組成要素
*   **動效與過場：** 透過 SwiftUI 的先進轉場（如 `Zoom` 轉場），讓互動過程變得順暢且具備品牌質感。
*   **無障礙與動態字級 (Dynamic Type)：** 品牌字型必須支援系統的 Dynamic Type，確保在不同字級下排版仍具可讀性。
*   **色彩使用：** 保持克制，將顏色運用在層級區分、反饋狀態及選取狀態（Tint Color）。

---

## 3. 技術實作建議

### 推薦技術與框架
*   **SwiftUI：** 官方強烈建議使用 SwiftUI 進行開發，因為它能提供開箱即用的原生組件與互動行為，大幅降低自定義組件的維護成本。
*   **SF Symbols：** 擁有超過 7,000 個符號，具備優秀的縮放性與動態字級適配，建議優先使用而非自行開發圖示。
*   **Context Menus：** 運用內建的 Context Menus 處理全域動作，其內建的形變動畫（Morphing Animation）是原生體驗的關鍵，無需自行手刻。

### 不建議的模式
*   **強行客製化通用元件：** 如非必要，不要自定義基礎的按鈕大小或彈出視窗行為，這會導致 App 顯得「過時」或「操作生澀」。
*   **全域性 Logo 展示：** Logo 應放在次要位置，避免佔用重要螢幕空間，重點在於讓使用者「沈浸於內容」而非「被品牌打擾」。

---

## 4. 給開發者的實用重點（Key Takeaways）

1.  **審核你的組件 (Audit Components)：**
    *   將「功能性」強的介面（如設定頁、導航欄）交還給系統標準組件。
    *   將「品牌影響力」大的區域（如產品展示、動態牆）規劃為自定義開發重點。

2.  **深色模式 (Dark Mode) 是必須的：**
    *   不要只設計一套 UI。如果 App 不支援深色模式，使用者體驗會大打折扣，直接影響對品牌的評價。

3.  ** Typography 的適配性：**
    *   若使用 Custom Font，必須確保其在 `Dynamic Type` 的 `Accessibility` 級別下，不會發生截斷（Truncation），而是自動斷行或調整佈局。

4.  **一致性與平台差異：**
    *   即便品牌在不同平台（Web/Android）上有統一的視覺規範，也要針對 iOS 進行「轉譯」。例如：即使 Share 圖示使用自定義風格，也必須符合 iOS 使用者對分享按鈕位置與行為的預期。

5.  **效能至上：**
    *   過多的自定義動畫若導致掉幀（Dropped frames），會直接傳遞給使用者「品質不佳」的負面感受。確保所有自定義動效在各種裝置上皆流暢。

> **總結建議：** 品牌識別的極致不是「無所不在」，而是「恰到好處」。透過遵守 iOS 的導航框架，並將創意與色彩揮灑在內容層，開發者能在維持高效使用體驗的同時，讓產品脫穎而出。

<!-- resources -->

---

## 資源連結

- [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/251/4/52fb4c75-99ba-419f-90d6-bfef374ac966/downloads/wwdc2026-251_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/251/4/52fb4c75-99ba-419f-90d6-bfef374ac966/downloads/wwdc2026-251_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/251/
