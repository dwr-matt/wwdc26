# What's new in Xcode 27

這是一份關於 WWDC 2026 Session「What's new in Xcode 27」的詳細技術摘要。

---

# WWDC 2026 技術摘要：Xcode 27 新功能詳解

## 概述
Xcode 27 在開發體驗、工作流程客製化、AI 輔助編碼以及應用程式生命週期管理上進行了重大升級。本 Session 深入探討了如何透過模組化的工作空間、強大的 Coding Agents、全新的 Device Hub 以及強化的 Organizer 與 Instruments 工具，協助開發者從原型設計到產品發布的全流程中，更高效地開發與優化高品質應用程式。

---

## 主要更新重點

### 1. 工作空間與自訂化 (Workspace & Customization)
*   **介面重構**：工具列（Toolbar）經過重新設計，將跳轉欄（Jump Bar）的功能（如歷史記錄、編輯器控制項）整合至工具列。
*   **模組化視圖**：導入了三路選擇器（Three-way Chooser），可快速切換：
    *   畫布模式（Canvas）：預覽與 Playground。
    *   助理編輯器（Assistant Editor）：顯示相關程式碼。
    *   版本控制模式（Source Control）：審閱變更。
*   **動態主題系統**：提供全新的設定介面，可透過滑塊調整背景強度、文字顏色與漸層，並支援為不同的專案配置專屬主題，方便在多開視窗時進行視覺區隔。
*   **非干擾性錯誤顯示**：即時預測錯誤（Predictive issues）現在以更柔和的背景顯示，減少開發者在打字時的干擾，待編譯後再轉為正式的警示色彩。

### 2. Coding Agents 的深度整合
*   **編輯器內嵌體驗**：Coding Agents 的對話記錄現在直接作為編輯器面板的一部分，支援與現有的拆分視圖、標籤頁靈活整合。
*   **AI 規劃與實作**：使用 `slash plan` 指令，代理程式會先收集背景資訊並擬定執行計畫，開發者可進行審核或提供指導。
*   **多工處理**：側邊欄現在包含多個並行任務的追蹤，可以隨時切換查看代理程式生成的檔案、人工製品（Artifacts）或變更。

### 3. Device Hub：統一的裝置模擬與評測
*   **集中化管理**：無論是模擬器或實體裝置，均在 Device Hub 中統一管理。
*   **動態評測**：視窗支援縮放與快速操作（如旋轉、截圖）。開發者可以在此直接測試無障礙功能（如動態字體、高對比度）以及 iPhone Mirroring 的多種螢幕長寬比，確保適應性佈局。

### 4. 國際化 (Localization)
*   **Agent 輔助翻譯**：Coding Agents 可讀取程式碼，自動建立字串目錄（String Catalog）並完成多語言翻譯，同時考量專案內容與語系指南。
*   **生成式翻譯**：點選「Generate translations」即可在後台進行特定語言的自動翻譯，並透過 TestFlight 收集原生使用者的回饋。

### 5. Organizer 與性能診斷
*   **整合儀表板**：將診斷資訊與效能指標合併於單一視圖。
*   **新指標**：
    *   **Storage Metric**：分析二進位檔案、資料與文件大小，並提供壓縮建議。
    *   **Hitches Metric**：不僅限於滑動，還涵蓋 Liquid Glass 與 SwiftUI 動畫中的卡頓追蹤。
*   **績效目標與建議**：Organizer 現在會根據同類應用程式的基準，提供可執行的性能優化目標（如掛起率、磁碟寫入量），並能使用 Coding Agents 針對診斷資料生成具體的修復建議。

---

## 關鍵技術與實用程式碼模式

*   **Top Functions 分析**：在 Instruments 中使用「Top Functions」功能，能快速定位 CPU 消耗最高的程式碼區塊。
*   **Quick Open 捷徑**：使用 `Command + Shift + O` 不僅能快速打開檔案，還能直接跳轉至函數定義或特定程式碼區塊，大幅提升導航速度。
*   **Untitled Projects**：Xcode 27 允許在不命名、不建立專案檔案的情況下直接開啟空白畫布進行原型驗證，隨後可選擇存檔或捨棄，非常適合短暫的測試想法。

---

## 開發者實用建議

1.  **善用多重主題**：若同時處理多個功能相似的專案，為每個專案設定不同的配色主題，能有效避免誤操作。
2.  **先規劃後編碼**：在使用 AI 輔助編寫複雜邏輯時，養成使用 `/plan` 指令的習慣，這能確保代理程式理解全域環境，減少無效的程式碼迭代。
3.  **監測儲存空間**：在發布前務必查看 Organizer 的 Storage 報告，許多效能與下載轉換率的問題其實源於未壓縮的資源檔案。
4.  **持續整合**：儘早配置 Xcode Cloud，將單元與 UI 測試自動化。這是在交付頻繁更新時，確保不發生回歸（Regression）的最強防線。

---

**推薦延伸 Session：**
*   *Xcode Agents and You*：深入探討代理工作流。
*   *Get the Most out of Device Hub*：評測與模擬器操作進階技巧。
*   *Translate Your App Using Agents in Xcode*：國際化與字串目錄處理。
*   *Build, Deliver, and Automate with Xcode Cloud*：自動化部署的最佳實踐。

<!-- resources -->

---

## 資源連結

- [Xcode updates](https://developer.apple.com/documentation/Updates/Xcode)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/258/4/66bc9c90-649b-4a16-a2bb-1e6f16b1ec73/downloads/wwdc2026-258_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/258/4/66bc9c90-649b-4a16-a2bb-1e6f16b1ec73/downloads/wwdc2026-258_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/258/
