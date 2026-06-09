# Modernize your UIKit app

這是一份為開發者整理的 **WWDC 2026: Modernize your UIKit app** 技術摘要。

---

# 技術摘要：Modernize your UIKit app (WWDC 2026)

## 概述
隨著 iOS 27 與 macOS 27 的推出，蘋果大幅強化了應用程式的「適應性 (Adaptivity)」。現今的應用程式必須能在多變的視窗環境（如 iPhone 鏡像輸出、iPad 隨意縮放視窗）中無縫運作。本場次重點介紹了如何從傳統的 UI 開發模式轉向動態、基於場景（Scene-based）的適應性佈局，並介紹了 Xcode 27 中強大的 AI 代理程式（Agentic Coding）如何協助開發者自動化處理繁瑣的遷移工作。

---

## 主要概念與框架介紹

### 1. 徹底的適應性要求 (App Adaptivity)
在 iOS 27 中，iPhone 應用程式在 Mac 鏡像模式下將實現「完全可調整大小」。這意味著：
*   **拋棄固定尺寸思維**：開發者不能再依賴 `main screen` 參考。
*   **Scene Lifecycle 為強制標準**：使用舊版 App Lifecycle 的應用程式將無法啟動。
*   **User Interface Idiom 與 Orientation 的弱化**：檢查 `.phone` 或 `.pad` 以及螢幕方向已不再是適當的佈局決定指標，取而代之的是 `Size Classes` 與視窗本身的有效幾何（Effective Geometry）。

### 2. UI 組件的更新
*   **Tab Bars**：iPhone 應用程式現在可以選用 Sidebar 模式（透過 `sidebarPreferredPlacement`），系統會根據橫向尺寸等級自動調整顯示與否。
*   **Navigation Bars**：新增 `barMinimizationBehavior` 屬性，可自訂捲動時導覽列的縮合行為。
*   **Menus**：針對 Apple Intelligence 進行優化，系統會自動在合適情境中加入「Ask Siri」選項。

### 3. Apple Intelligence 整合
*   **View Annotations API**：開發者可使用此 API 標記 View，讓 Siri 更精準地獲取應用程式內的實體（App Entities）。
*   **Drag & Drop 整合**：Siri 現在可以直接從你的 Drag 處理器中載入資源。注意，請避免在 `sessionWillBegin` 中觸發動畫或 Modal UI，應改至 `sessionDidMove` 執行。

---

## 建議的技術與實作模式

### 移除 Main Screen 依賴
*   **不要使用**：`UIScreen.main.bounds` 或 `UIScreen.main.scale`。
*   **改用**：
    *   `UITraitCollection` 的 `displayScale`（用於繪圖與縮放）。
    *   `UIWindowScene` 的 `effectiveGeometry`（用於判斷可用空間）。
    *   直接存取 `view.bounds` 或其 superview 的尺寸。

### Trait Changes 追蹤
*   **自動化追蹤 (Automatic Trait Tracking)**：在 `layoutSubviews`、`drawRect` 等方法中，系統會自動偵測變更並觸發重繪，無需手動監聽。
*   **手動監聽**：對於無法自動更新的部分，使用 `registerForTraitChanges` 閉包來處理快取失效或資料更新。

### 棄用舊屬性檢測
*   **移除**：`UIDevice.current.userInterfaceIdiom` 以及檢查 `UIDeviceOrientation`。
*   **替代**：使用 `Size Classes` 作為決定佈局的主要依據，並透過 `Window Scene` 的變更通知來獲取空間資訊。

---

## 開發者實用重點 (Action Items)

1.  **升級 Lifecycle**：確保專案已遷移至 `UISceneDelegate`。
2.  **善用 Xcode 27 新技能**：
    *   在 Xcode 27 中使用「App Modernization」代理功能，它可以自動將 `UIScreen` 呼叫改為 `TraitCollection` 或 `Scene` 檢查，甚至協助遷移 Lifecycle。
    *   若需將流程記錄為文件，可使用指令 `xcrun agent-skills export` 導出 Markdown 格式。
3.  **使用 DeviceHub 測試**：
    *   放棄在模擬器中來回切換裝置。
    *   開啟 Xcode 27 的 **DeviceHub**，使用「Enter Resize Mode」自由拖曳調整視窗大小，模擬真實的適應性測試場景。
4.  **檢查 Safe Area**：若自訂了避開 Safe Area 的邏輯，請設定 `barMinimizationSafeAreaAdjustment = .never` 以避免系統自動介入導致佈局衝突。
5.  **更新視覺設計**：檢查捲動時的 `scrollEdgeAppearance`，因為 iOS 27 更新了預設視覺樣式，可能與你自訂的 `soft` 或 `hard` 風格不再相符。

---

> **編輯註記**：這場 Session 是現代化 UIKit 應用的重要指南。對於已經遷移至 Scene-based 開發的團隊，重點應放在「移除對硬體層的絕對依賴」，並利用 Xcode 的 AI 技能來減少重複性維護成本。

<!-- resources -->

---

## 資源連結

- [TN3208: Preparing your app’s launch screen to meet App Store requirements](https://developer.apple.com/documentation/Technotes/tn3208-preparing-your-apps-launch-screen-to-meet-app-store-requirements)
- [TN3210: Optimizing your app for iPhone Mirroring](https://developer.apple.com/documentation/Technotes/tn3210-optimizing-your-app-for-iphone-mirroring)
- [Make your UIKit app more flexible](https://developer.apple.com/videos/play/wwdc2025/282/)
- [Adapting your app when traits change](https://developer.apple.com/documentation/UIKit/adapting-your-app-when-traits-change)
- [Transitioning to the UIKit scene-based life cycle](https://developer.apple.com/documentation/UIKit/transitioning-to-the-uikit-scene-based-life-cycle)
- [Automatic trait tracking](https://developer.apple.com/documentation/UIKit/automatic-trait-tracking)
- [Human Interface Guidelines: Menus](https://developer.apple.com/design/human-interface-guidelines/menus)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/278/4/8c3f2e61-52d3-4915-9543-96e2f13adc8b/downloads/wwdc2026-278_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/278/4/8c3f2e61-52d3-4915-9543-96e2f13adc8b/downloads/wwdc2026-278_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/278/
