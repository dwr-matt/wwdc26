# WidgetKit foundations

這是一份為開發者整理的 **WWDC 2026: WidgetKit Foundations** 技術摘要。

---

# WWDC 2026 Session 摘要：WidgetKit Foundations

## 概述
本 session 由 System Experience 團隊的 Jonathan Long 主講，旨在為開發者建立 WidgetKit 的核心知識體系。內容涵蓋了小工具（Widgets）的基本架構、如何透過 Timeline Provider 保持資料更新、如何整合 App Intents 實現互動與個人化，以及如何確保小工具在不同的系統環境（如 tinted mode）下保持最佳視覺呈現。

---

## 主要概念與架構

### 1. 小工具的核心價值
好的小工具應具備三大特質：
*   **一眼即知（Glanceable）：** 資訊簡潔，一眼即可獲取核心訊息。
*   **相關性（Relevant）：** 內容需根據時間、位置與使用者行為動態調整。
*   **個人化（Personalizable）：** 支援使用者設定，顯示對其最有意義的內容。

### 2. Widget 系統架構
*   **獨立進程：** Widget Extension 作為獨立於主 App 的進程運行。
*   **共享資料：** 必須透過 **App Group** 共享容器（如 Shared Database 或 UserDefaults）來傳遞資料。
*   **Timeline 模型：** WidgetKit 向 Extension 請求「Timeline」，由一系列的「Timeline Entry」組成。每個 Entry 包含特定時間點所需的視圖資料。

### 3. Timeline Provider 的三種狀態
*   **Snapshot：** 顯示在 Widget Gallery 中的預覽圖，需展現最佳化且具吸引力的內容。
*   **Placeholder：** 當資料載入中或系統需要瞬時顯示時的預留狀態，建議使用 `redacted` 修飾符。
*   **Timeline：** 包含多個 Entry 的序列，系統會按排程自動渲染。

---

## 程式碼模式與技術應用

### Widget 設定 (Configuration)
開發者需選擇適合的配置類型：
*   **StaticConfiguration：** 無需額外使用者設定的簡單 Widget。
*   **AppIntentConfiguration：** 支援使用者透過配置介面自訂內容。

### 關鍵修飾符與 API
*   **`.containerBackground(_:for:)`：** 標註 Widget 的背景視圖。這對於系統在「Tinted/Clear」環境下自動替換玻璃質感效果至關重要。
*   **`.widgetURL(_:)`：** 實現 Deep Link 的核心，點擊 Widget 後將 URL 傳遞給主 App，直接導航至特定內容頁。
*   **`.widgetAccentedRenderingMode(_:)`：** 解決在 Tinted 模式下圖片（如書封）變色失真的問題。使用 `.fullColor` 可強制保留原始顏色。

### Timeline 更新策略 (Reload Policy)
*   **At End：** 當所有 Entry 顯示完畢後觸發更新。
*   **After Date：** 在指定時間點強制觸發更新。
*   **Never：** 不自動更新，完全依賴 App 端的明確呼叫（`WidgetCenter` API 或 Push Notifications）。

---

## 開發者實用重點

1.  **善用 SwiftUI 預覽（Canvas）：** 在 Xcode 中直接測試不同 widget family、顏色配置（Color Schemes）與渲染模式，無需頻繁部署到實體機。
2.  **效能管理：** 
    *   WidgetKit 擁有更新預算（Budget）。應根據使用者的觀看習慣由系統自動調節。
    *   在 App 進入後台時，呼叫 `WidgetCenter` 進行 reload 是保持資料新鮮度的最佳時機。
3.  **互動式體驗：** 透過 App Intents，在 Widget 上加入按鈕或開關（Button/Toggle），實現「不開啟 App 即可完成操作」的流暢體驗。
4.  **測試清單：**
    *   在開發過程中開啟 **WidgetKit Developer Mode**，以解除 reload 預算限制，加速迭代。
    *   務必測試在「彩色」、「Tinted」、「Clear」模式下的視覺效果。
    *   對於複雜或具有即時性（如運動賽事）的需求，應考慮改用 **Live Activities**。
5.  **支援多樣尺寸：** 盡可能支援多種 Family（包含新推出的 `systemExtraLarge`），讓使用者能靈活運用桌面空間。

---

*資料來源：WWDC 2026 - WidgetKit Foundations*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/277/4/e9dd0c7d-3a2e-4cf3-9e65-c9cba19d3616/downloads/wwdc2026-277_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/277/4/e9dd0c7d-3a2e-4cf3-9e65-c9cba19d3616/downloads/wwdc2026-277_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/277/
