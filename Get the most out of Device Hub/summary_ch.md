# Get the most out of Device Hub

這是一份關於 WWDC 2026 Session「Get the most out of Device Hub」的詳細技術摘要。

---

# WWDC 2026 技術摘要：充分利用 Device Hub

## 概述
隨著應用程式需支援的螢幕尺寸、字體大小、深色模式與環境設定日益複雜，開發者在多裝置間的測試負擔也隨之增加。**Device Hub** 是 Xcode 27 隨附的一款全新獨立應用程式，旨在成為管理實體設備與模擬器的單一中樞。它提供了統一的視覺化介面，讓開發者能進行跨裝置的互動、設定調整、診斷資訊收集以及應用程式生命週期管理，藉此優化開發與除錯工作流程。

---

## 主要概念與功能架構

Device Hub 將功能劃分為三大核心區域：

### 1. 畫布 (Canvas)：互動與控制
*   **即時螢幕同步**：提供裝置螢幕的即時互動畫面，支援滑鼠點擊、捲動與觸控手勢。
*   **情境感知控制 (Contextual Controls)**：底部工具列會根據裝置類型自動變更（如 Apple TV 的導航鍵、Apple Watch 的數位錶冠、Vision Pro 的環境/相機控制）。
*   **進階檢視選項**：包含縮放、一對一物理尺寸 (1:1 scale)、以及「調整大小模式」(Resize Mode)，方便開發者測試自適應佈局。
*   **鍵盤輸入重定向 (Capture Keyboard)**：可將 Mac 的鍵盤輸入直接發送至裝置，簡化快捷鍵測試。

### 2. 側邊欄 (Sidebar)：組織與管理
*   **統一清單**：集中顯示所有已連接的實體裝置與模擬器。
*   **篩選與分組**：支援自訂排序與篩選，便於管理大量裝置庫。
*   **快速存取**：支援「多視窗模式」，可同時開啟多個裝置視窗進行螢幕對比。
*   **操作指令**：透過右鍵點擊可進行重啟、配對等快速操作。

### 3. 檢查器 (Inspector)：配置與診斷
分為五大面板，集中管理裝置設定：
*   **裝置設定**：即時調整深色模式、動態字體大小 (Dynamic Type)、地點模擬等。
*   **診斷報告**：聚合系統日誌、Crash 報告與 Spin log，協助快速釐清當機原因。
*   **裝置資訊**：儲存空間、型號、序列號等硬體概況。
*   **應用程式管理**：安裝/卸載應用程式，並可匯出或替換 App 的資料容器 (Data Container)。
*   **設定檔 (Profiles)**：管理組態描述檔 (Configuration) 與部署描述檔 (Provisioning Profiles)。

---

## 關鍵技術 workflow 示範

在本次示範中，開發者展示了如何透過 Device Hub 復現一個在特定條件下 UI 被截斷的 Bug：

1.  **配對與環境設定**：使用 `Pair nearby device` 功能配對實體 Apple Watch，並拖入 Core Location 組態檔案以模擬特定地點。
2.  **診斷收集**：透過 UI 直接截圖 (Screenshot) 並觸發 `SysDiagnose` 獲取系統層級日誌。
3.  **資料容器交換**：將實體機上的 App 資料容器匯出，並在模擬器上透過「檢查器」面板匯入，確保模擬環境的數據與錯誤發生時一致。
4.  **環境鏡像 (Mirroring)**：在模擬器中手動設定橫向模式 (Landscape)、調整字體大小，成功復現「特定海拔地點 + 特大字體 + 橫向模式」下的 UI 截斷問題。

---

## 開發者實用重點 (Key Takeaways)

*   **跨平台一致性**：無論是模擬器還是實體機，Device Hub 提供完全一致的操作體驗，減少切換工具的認知成本。
*   **資料容器除錯**：善用資料容器的匯入/匯出，可以精準復現生產環境中特定使用者遇到的數據相關錯誤。
*   **CLI 自動化工具 (Device Cuttle)**：
    *   對於 CI/CD 環境或自動化指令稿，Apple 提供了 **`Device Cuttle`** 命令列工具。
    *   該工具共享 Device Hub 的底層技術，支援 JSON 輸出，可用於自動化部署 App、變更裝置設定及批量提取診斷資料。
*   **相容性建議**：若您的 App 正在進行 UI 優化，建議配合此 Session 觀看 *「Modernize your UIKit app」*，進一步了解如何處理自適應佈局 (Resizability)。

---
*註：Device Hub 已隨 Xcode 27 正式發布，開發者無需啟動 Xcode 即可透過該獨立應用程式管理開發環境。*

<!-- resources -->

---

## 資源連結

- [Device Hub](https://developer.apple.com/documentation/Xcode/device-hub)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/260/4/87d4b48f-1dfb-413f-a4f8-44d80b0f3432/downloads/wwdc2026-260_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/260/4/87d4b48f-1dfb-413f-a4f8-44d80b0f3432/downloads/wwdc2026-260_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/260/
