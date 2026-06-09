# Rev up your CarPlay app

這份技術摘要旨在協助開發者快速掌握 iOS 27 中 CarPlay 的核心更新與新功能。

---

# WWDC 2026 技術摘要：Rev up your CarPlay app

## 概述
本場 Session 全面介紹了 iOS 27 為 CarPlay 帶來的重大更新，重點在於提升使用者體驗與擴展應用場景。本次更新主要涵蓋了三個核心領域：**支援車內影片瀏覽**、**增強 UI 框架（包括 Mini-player 與語音控制）**，以及**導航 App 的進階互動能力（面板控制與路線共享）**。開發者現在可以透過全新的 API，在 CarPlay 上打造更豐富、互動性更強的應用程式。

---

## 主要概念與 API 更新

### 1. 影片瀏覽與播放 (Video in Car)
iOS 27 允許具備「影片播放」功能的車輛在 CarPlay 上瀏覽影片內容。
*   **整合方式**：若 App 原本支援 AirPlay 影片串流，則無需大改。開發者只需使用 `CarPlay framework` 實作瀏覽介面。
*   **Entitlements**：若內容同時包含聲音與影片，建議同時申請 `CarPlay audio` 與 `CarPlay video` 權限，確保在不支援影片播放的車輛中，App 仍能以音訊形式存在。
*   **狀態處理**：當車輛指示無法播放影片時，系統會自動切換為音訊播放（例如播客或體育廣播）。

### 2. CarPlay UI 框架增強
*   **列表與縮圖 (List & Thumbnails)**：支援縱向與橫向比例圖片，並新增覆蓋層 (Overlays) 支援（例如顯示直播狀態、體育賽事比分）。
*   **Details Header**：用於在列表上方突出顯示單一項目（如：正在播放的劇集、電影摘要），結合標題、縮圖與操作按鈕。
*   **Mini-player**：當用戶播放內容時，現在支援自動顯示 Mini-player。開發者可透過 `NowPlayingTemplate` 的 `allowsMiniPlayer` 屬性設定是否啟用。
*   **語音控制 (Voice Control)**：現已開放給所有類型的 CarPlay App。支援 Overlay 形式展示，讓使用者在操作語音對話的同時，仍能看到導航地圖。

### 3. 導航 App 進階功能
*   **面板 (Panels)**：允許開發者在地圖模板的「主要介面區」建立自定義 UI（如顯示路徑、景點資訊），且不影響導航流程。
*   **路線共享 (Route Sharing)**：App 可將路線座標分享給車輛系統。這對於電動車（自動規劃充電站）或支援駕駛輔助系統（ADAS）的車輛至關重要，車輛能根據路線資訊自動調整效能或導航建議。

---

## 技術實作與模式

### 影片瀏覽 UI 實作邏輯
開發者需在 `CPConfiguration` 中檢測車輛是否支援影片，進而動態決定是否顯示「影片」標籤頁：
```swift
// 檢查設備是否支援影片
if CPInterfaceController.shared.sessionConfiguration.supportsVideo {
    // 新增影片標籤頁
    self.tabBar.add(videoTemplate)
}
```

### 處理播放狀態 (Playback Configuration)
透過 `CPPlaybackConfiguration` 提供 metadata：
*   **Preferred Presentation**：設定為 `.video` 或 `.audio`。
*   **進度顯示**：更新 `elapsedTime` 與 `duration`，系統會自動在縮圖與 Header 顯示進度條。
*   **同步更新**：當播放狀態（如暫停、恢復）改變時，必須即時更新設定，以保持 UI 的準確性。

### 語音控制與 Overlay
在導航地圖上使用語音控制覆蓋層：
```swift
// 顯示語音控制 Overlay
let voiceTemplate = CPVoiceControlTemplate(...)
interfaceController.present(voiceTemplate, asOverlay: true)
```

---

## 開發者實用重點 (Key Takeaways)

1.  **善用 CarPlay Simulator**：新的模擬器現已整合在 **Device Hub** 中，支援多種螢幕解析度與車輛配置測試，是除錯的最佳工具。
2.  **優先考慮語音互動**：對於導航或資訊類 App，善用 `CPVoiceControlTemplate` 提供的 Action Buttons（如「撥打電話」、「開始導航」），這能顯著降低駕駛的認知負載。
3.  **優化縮圖資訊**：利用 `CPThumbnailOverlay` 為直播影片、新內容添加視覺標記，能顯著提升內容發現率。
4.  **電動車整合 (Route Sharing)**：若您的 App 涉及長途路線規劃，請務必實作路線共享 API。這不只是為了導航，更是為了讓車輛能即時計算能量損耗並自動插入必要的充電行程。
5.  **測試環境建置**：影片 App 開發者請下載 `Additional Tools for Xcode` 中的 CarPlay Simulator 套件，針對「支援影片的車輛」配置進行 UI 驗證。

透過這些新功能，開發者可以讓 CarPlay 應用程式從單純的輔助工具，轉變為深度結合車輛情境的駕駛助手。

<!-- resources -->

---

## 資源連結

- [CarPlay for developers](https://developer.apple.com/carplay)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/212/4/c594f5de-1012-4f5a-bad4-95ca200f5f58/downloads/wwdc2026-212_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/212/4/c594f5de-1012-4f5a-bad4-95ca200f5f58/downloads/wwdc2026-212_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/212/
