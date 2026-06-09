# Build, deliver, and automate with Xcode Cloud

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 關於 **ExoCloud**（原 Xcode Cloud）的最新功能與實踐指南。

---

# 技術摘要：使用 ExoCloud 構建、交付與自動化工作流

## 1. 概述
本場 Session 介紹了如何透過 ExoCloud 強化 Apple 平台的持續整合與交付（CI/CD）流程。內容涵蓋了從基礎的 App 上線（Onboarding）、設定多平台（iOS/macOS）工作流、自動化分發至 TestFlight，到進階的 Webhook 自動化與多儲存庫（Multi-repository）管理。目標是讓開發者透過雲端自動化測試與構建，釋放本地端資源，並確保 App 在上架前的穩定性。

---

## 2. 主要概念與技術亮點

*   **雲端構建與測試（Cloud-based CI/CD）：**
    *   ExoCloud 在雲端虛擬機（Ephemeral VMs）上運行構建與測試，完全隔離於本地環境。
    *   **安全性：** 源碼僅在構建時拉取，構建結束後即刪除，Apple 不會儲存任何原始程式碼。
    *   **並行處理：** 支援在不同裝置與 OS 版本上進行並行測試，大幅縮短測試週期。
*   **分發自動化（Distribution Automation）：**
    *   原生整合 TestFlight 與 App Store Connect。
    *   透過助理（Assistant）簡化 App 記錄創建、Bundle ID 及 SKU 的註冊流程。
*   **Webhook 自動化：**
    *   支援在構建的各個生命週期（創建、啟動、完成）觸發 Webhook，方便整合外部監控儀表板。
*   **儲存庫管理（Repository Management）：**
    *   支援專案依賴多個儲存庫，適用於模組化開發（如將共用 Framework 獨立為專案）。

---

## 3. 實作流程與技術模式

### A. 快速上線 (Onboarding)
在 Xcode 中進入 **Report Navigator** -> **Cloud** 頁籤，即可啟動自動化設定助理。
1.  **授權與連結：** 連結原始碼供應商。
2.  **自動生成：** 系統會自動掃描專案並產生預設的工作流（Workflow）。
3.  **多平台支援：** 若專案包含 macOS 與 iOS 雙平台，可在 Workflow Manager 中為不同 Target 創建獨立的工作流。

### B. 分發設定 (Setup Distribution)
當 App 需要交付測試時，透過右鍵點擊 App 選取 **Setup Distribution**：
*   **必要屬性：** 需定義 App 名稱、Bundle ID 及 SKU。
*   **自動化歸檔：** 建立專用的「Archive」Action，這是部署至 TestFlight 的核心步驟。

### C. 進階自動化 (Webhooks)
透過 **Manage Webhooks** 介面設定：
*   **Payload URL：** 指定接收構建狀態資訊的公開端點。
*   **監控：** 可在 Webhook 列表中檢視歷史交付狀態（成功為綠色標示）。

### D. 多儲存庫整合
針對拆分出來的模組化代碼：
*   進入 **Manage Repositories**。
*   點擊「Add」並輸入 Git 遠端 URL。ExoCloud 會自動將該儲存庫納入構建依賴，確保每次構建都包含最新的共用模組程式碼。

---

## 4. 開發者實用重點 (Takeaways)

1.  **別讓本地端成為瓶頸：** 當測試套件（Unit Test）運行時間過長時，應果斷遷移至 ExoCloud。這不僅能節省開發時間，還能透過雲端並行測試捕捉更多邊緣案例。
2.  **善用 Workflow Manager：** 不要只依賴預設配置。透過自訂工作流，你可以針對不同的分支（如 `develop` vs `main`）設定不同的構建指令與測試範圍。
3.  **安全性考量：** 了解 ExoCloud 的虛擬機是「臨時性」的，這對強調隱私與資安的專案來說是核心價值。
4.  **擴充生態系：** 透過 Webhook 串接 Slack、Discord 或自建的分析儀表板，能讓團隊成員在第一時間接收構建狀況與 Bug 報告，縮短除錯反饋迴圈。
5.  **模組化擴展：** 當專案增長時，盡早將共用邏輯拆分為獨立 Repo，並透過 ExoCloud 的多儲存庫功能進行管理，能提升維護效率。

---

### 推薦進階學習 Session：
*   *Create Practical Workflows in ExoCloud*
*   *Extended ExoCloud Workflows*
*   *Simplify Distribution in ExoCloud*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/261/7/35c49f2b-3f0a-4956-826b-d54d9fed678e/downloads/wwdc2026-261_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/261/7/35c49f2b-3f0a-4956-826b-d54d9fed678e/downloads/wwdc2026-261_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/261/
