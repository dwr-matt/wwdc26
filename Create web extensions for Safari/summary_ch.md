# Create web extensions for Safari

這份技術摘要旨在幫助開發者快速掌握 Safari Web Extension 的開發、測試與發布流程。

---

# WWDC 2026 技術摘要：Create web extensions for Safari

## 概述
本場 Session 由 Safari 團隊工程師 Kiara 主講，旨在介紹如何從零開始建構、開發、測試並發布 Safari Web Extension。課程重點在於利用標準化的 W3C Web Extensions API，將網頁應用（HTML, CSS, JS）轉化為可在 iOS、iPadOS、macOS 及 VisionOS 上運行的瀏覽器擴充功能。內容涵蓋內容阻擋（Content Blocking）、權限管理、資料持久化以及與原生 App 進行「原生通訊（Native Messaging）」。

---

## 主要概念、API 與框架

### 1. 核心結構
*   **Manifest (manifest.json)**：擴充功能的「身分證」，定義名稱、版本、圖示、權限及各類功能連結。
*   **封裝要求**：Safari Web Extension 必須封裝在一個包含它的 App（Containing App）中才能發布到 App Store。

### 2. 關鍵 API
*   **Declarative Net Request API**：用於攔截、修改或重新導向網路請求（如封鎖廣告、強制重新導向網站）。
*   **Scripting API (registerContentScripts)**：允許動態注入內容腳本（Content Scripts），支援在頁面上執行 JS 和 CSS。
*   **Storage API**：使用 `localStorage` 進行跨重啟的資料持久化儲存。
*   **Native Messaging**：實現 Web Extension 與宿主 App 之間的通訊，允許擴充功能呼叫系統級原生功能（如 Bio-authentication）。

---

## 示範技術與模式

### 規則定義 (Rules)
*   **靜態規則**：直接定義於 manifest 中。
*   **動態規則**：透過 `updateDynamicRules` API 在執行階段根據用戶行為（如新增阻擋名單）即時更新。

### 權限管理模型
*   **可選主機權限 (Optional Host Permissions)**：尊重用戶隱私，擴充功能不應預設擁有所有網站存取權。透過 `permissions` 要求存取權限，Safari 會在觸發時顯示權限請求，並在圖示上顯示顏色提示用戶。

### 跨元件通訊模式
*   **Native Messaging 流程**：
    1.  Web Extension 的 JS 發送訊息。
    2.  App Extension（位於 Xcode 專案中）攔截訊息。
    3.  原生 App 處理請求（如：進行 Touch ID 驗證）。
    4.  回傳結果給 Extension。

---

## 開發者實用重點

### 1. 開發環境設定
*   開啟「Show Features for Web Developers」並允許「Unsigned Extensions」，以便在 Safari 設定中直接載入本地開發資料夾。

### 2. 進階工具
*   **Safari Web Extension Packager**：利用 Terminal 指令即可快速生成 Xcode 專案結構，方便進行原生功能串接。
*   **TestFlight**：強烈建議使用 TestFlight 進行 beta 測試，蒐集用戶反饋後再進行正式的 App Store 提交。

### 3. 資料與腳本生命週期
*   **更新時的維護**：切記 `registeredContentScripts` 不會跨越「擴充功能版本更新」而自動保留。開發者需在 `onInstalled` 事件中偵聽更新，並重新註冊內容腳本。
*   **Background Page**：適合處理擴充功能生命週期事件、監聽瀏覽器事件以及管理跨模組訊息傳遞。

### 4. 發布策略
*   **App Store Connect**：現在支援不透過 Mac，直接在瀏覽器上完成基本的擴充功能打包與提交。
*   **多平台支援**：只要符合 Web 標準，開發者製作的 Extension 可無縫擴展至 Apple 全生態系統（包含 VisionOS）。

---

## 參考資源
*   **官方範例代碼**：請下載 Session 附帶的 Sample Code 以實作內容阻擋與定時器邏輯。
*   **標準文件**：參閱 MDN 的 [Web Extensions Cross-browser documentation](https://developer.mozilla.org/)。
*   **回饋機制**：使用 [Feedback Assistant](https://feedbackassistant.apple.com/) 或在 [bugs.webkit.org](https://bugs.webkit.org/) 提交相關問題。

---
*編輯建議：開發者若已擁有其他瀏覽器的 Web Extension，可直接參考 Session 中的「Packaging and Distribution」章節，利用標準化 API 快速遷移至 Safari。*

<!-- resources -->

---

## 資源連結

- [w3.org — W3C WebExtensions Community Group](https://www.w3.org/community/webextensions/)
- [Packaging and distributing Safari Web Extensions with App Store Connect](https://developer.apple.com/documentation/SafariServices/packaging-and-distributing-safari-web-extensions-with-app-store-connect)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [MDN Web Docs - Web Extensions API](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/216/5/4fceecc8-1e28-465c-b894-fd0d03067c18/downloads/wwdc2026-216_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/216/5/4fceecc8-1e28-465c-b894-fd0d03067c18/downloads/wwdc2026-216_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/216/
