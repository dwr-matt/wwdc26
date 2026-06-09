# What's new in managing Apple devices

這是一份為開發者整理的 **WWDC 2026: What's new in managing Apple devices** 技術摘要。

---

# WWDC 2026 技術摘要：Apple 裝置管理的新變革

## 概述
本場 Session 由 Apple 裝置管理團隊工程師 Cyrus DeBoo 主講，重點介紹了 Apple 平台在企業與教育領域的管理新功能。核心訊息是：**「宣告式管理 (Declarative Management)」已成為行業標準**，本次更新圍繞著提升管理自動化、增強安全性、優化身分驗證以及教育場景的靈活性進行了深度擴展。

---

## 主要概念與技術框架

### 1. 宣告式管理 (Declarative Management) 的全面落地
Apple 強調宣告式管理已正式進入生產環境。本次更新重點包括：
*   **託管遷移 (Managed Migration)：** 透過宣告式配置，IT 可在裝置註冊後自動引導遷移資料，並鎖定特定安全性與隱私設置。
*   **狀態頻道 (Status Channel) 擴充：** 支援即時監控裝置狀態，如：裝置系統健康監控（Baseband、鏡頭、Face ID/Touch ID 等硬體狀態）、鎖定模式 (Lockdown Mode) 狀態及內容快取 (Content Caching) 狀態。
*   **憑證管理優化：** 支援多對多關係，透過宣告式資產 (Assets) 集中管理憑證，更新憑證時無需重新部署整個設定檔。

### 2. Apple Business 與 Apple School Manager
*   **平台整合：** Apple Business 平台已擴展至全球 200 多個國家與地區。
*   **全新 API：** 提供自動化管理 API，包含建立藍圖 (Blueprints)、設定檔管理、使用者與群組操作、以及審計事件 (Audit Events) 獲取。
*   **訂閱管理：** 引入新的大量授權機制，允許 IT 管理員購買並透過裝置管理服務 (MDM) 分配 App Store 訂閱。

### 3. App 與隱私權管理
*   **隱私共識流程：** 推出「統一隱私許可提示 (Consolidated Privacy Consent Prompt)」，當 App 或 Safari 網站首次要求存取相機、麥克風或位置時，IT 可提供自訂的「請求理由說明」，使用者點擊「允許」後，系統將統一應用權限，減少重複彈窗。
*   **二進位執行控制 (Binary Execution Control)：** 透過 Endpoint Security 框架，IT 可在 macOS 27 上宣告式地允許或拒絕特定二進位檔案的執行，並可強制終止違規程序。
*   **Package 管理：** 在移除套件配置時，可選擇自動刪除該套件安裝的所有殘留檔案與目錄。

### 4. 身分驗證 (Platform SSO)
*   **Web 認證選項：** macOS 27 引入基於 Web 的身分驗證，支援登入視窗與螢幕解鎖。這允許企業自訂登入介面，並支援多因子驗證 (MFA)、QR Code 免密碼登入（如醫療、零售業場景）。
*   **Touch ID 強制性：** IT 現在可要求在組織裝置上強制使用 Touch ID 作為登入、解鎖及 FileVault 解鎖的第二因子。
*   **Authenticated Guest Mode：** 允許訪客使用者（如醫護人員）透過受認證帳號登入共享 Mac，並支援解鎖 FileVault，確保資料安全性。

---

## 開發者實用重點與技術模式

*   **實作宣告式狀態：** 開發者應利用 `Status Channel` 訂閱裝置狀態變更。當裝置硬體發生變化（如相機故障）或狀態更新時，系統會自動將資料推送至伺服器，無需頻繁輪詢 (Polling)。
*   **利用 Managed App Framework：** Apple 強烈建議企業應用開發者採用 Managed App 框架，以支援硬體綁定金鑰 (Hardware-bound keys) 與裝置認證 (Attestation)，這對於企業級應用的安全部署至關重要。
*   **增強型日誌收集 (Enhanced Log Collection)：** 使用 `Trigger Enhanced Log Collection` 命令，可協助 AppleCare 遠端蒐集診斷資訊。
*   **教學應用開發：** Classroom App 引入了「引導式瀏覽 (Guided Browsing)」，開發者在設計教育類 App 時，應考慮如何與 Classroom 的鎖定機制配合。

---

## 資源建議
*   **文件與 Schema：** 所有裝置管理物件 Schema 與技術文件已更新至 [developer.apple.com](https://developer.apple.com) 及 Apple 的 GitHub 專案。
*   **相關 Session：**
    *   *Offer Subscriptions to Groups and Organizations* (了解訂閱分發)
    *   *App-A-Test* (了解企業應用認證)
    *   *Assessment Mode* (了解考試模式 App 開發)

---

**技術編輯小結：** 本次更新對 MDM 廠商與身分提供商 (IdP) 提出了更高的技術要求。開發者應立即開始整合新的 API（特別是 Web 認證與狀態頻道），以確保其產品符合 macOS 27 與後續系統的安全性標準。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/206/4/e49f983e-700d-4d52-ae6b-a0fa1ea89fd0/downloads/wwdc2026-206_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/206/4/e49f983e-700d-4d52-ae6b-a0fa1ea89fd0/downloads/wwdc2026-206_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/206/
