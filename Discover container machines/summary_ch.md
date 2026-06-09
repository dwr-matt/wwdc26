# Discover container machines

這是一份關於 WWDC 2026 Session「Discover Container Machines」的詳細技術摘要，專為開發者整理。

---

# WWDC 2026 技術摘要：Discover Container Machines

## 概述
本場次介紹了 Apple 在 **Containerization 框架**之上推出的全新功能——**Container Machine**。Container Machine 旨在為開發者提供一個既擁有容器（Container）的輕量、快速特性，又具備虛擬機（VM）持久性（Persistence）的 Linux 開發環境。此功能深度整合於 macOS，使跨平台開發（尤其是針對 Linux 伺服器應用）變得更加無縫與高效。

---

## 主要概念與框架

### 1. 核心技術基礎：Containerization 框架
*   **起源**：於 WWDC25 開源，是一個專為在 macOS 上運行 Linux 容器而設計的 Swift 框架。
*   **優勢**：提供基於 VM 的高效隔離，具備「亞秒級」的啟動速度。
*   **組成**：包含用於存儲、網路、執行與 Linux init 系統的 API。

### 2. 何謂 Container Machine？
它是 Containerization 框架的延伸應用，設計原則如下：
*   **輕量與快速**：像容器一樣輕盈，便於建立與銷毀。
*   **持久性（Persistence）**：像 VM 一樣具備狀態，離開後再回來，環境設定與檔案內容皆會保留。
*   **原生整合**：自動處理使用者映射、檔案系統共享，並能無縫切換終端機上下文。
*   **OCI 相容**：使用與 Container 相同的 OCI 映像檔格式（如 Alpine），確保生態系的一致性。

---

## 關鍵技術與操作模式

### 1. 生命週期管理 (Container Tool CLI)
開發者透過 `Container Tool` 進行管理，指令與標準容器工具非常相似：

*   **建立機器**：
    ```bash
    container-machine create --name my-dev-env
    # 可設定為預設機器，省去後續指令輸入名稱
    ```
*   **執行指令**：
    ```bash
    # 在容器機器中執行特定指令
    container-machine run my-dev-env uname -a
    
    # 進入互動式 Shell
    container-machine run
    ```
*   **查看資訊**：
    ```bash
    container-machine list
    # 顯示 IP 位址與資源使用狀況
    ```

### 2. macOS 與 Linux 的無縫協作
*   **自動化映射**：Container Machine 會自動同步 macOS 的使用者名稱與工作目錄，無需重複設定權限。
*   **檔案同步**：在 macOS 的 Xcode 或其他編輯器修改檔案後，Container Machine 內可即時存取。
*   **網路整合**：支援容器內的服務（如 Vapor Web Server）透過 IP 與 Port 直接映射到 macOS 的 Safari 瀏覽器中進行調試。

---

## 開發者的實用重點

### 為什麼要使用 Container Machine？
1.  **避免依賴衝突**：每個專案可以擁有獨立的 Linux 環境，擁有各自的 Toolchain 與依賴庫，不會污染宿主機或彼此干擾。
2.  **持久化開發體驗**：不再需要像純容器那樣每次重啟都要重新安裝套件或配置環境，大幅減少環境重建成本。
3.  **無感切換**：從 macOS 終端機進入 Linux 環境時，目錄位置與使用者身分保持一致，大幅降低 Context Switch（上下文切換）的認知負擔。
4.  **端到端調試**：對於 Server-side Swift（如 Vapor）開發者，可以在 macOS 上編輯、在 Linux 上編譯運行，並直接透過宿主機瀏覽器驗證結果，形成完整的閉環開發流程。

### 下一步建議
*   **取得工具**：前往 [GitHub](https://github.com/) 下載最新版本的 **Container Tool**。
*   **參考資源**：若想深入了解底層架構，建議回顧 WWDC25 的 "Meet Containerization" session。

---
*編輯備註：Container Machine 為跨平台開發者提供了極大的便利，特別是在處理需要 Linux 原生執行環境的專案時，它是目前 macOS 上最接近「原生開發體驗」的解決方案。*

<!-- resources -->

---

## 資源連結

- [Container](https://github.com/apple/container)
- [Containerization](https://github.com/apple/containerization)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/389/4/8dd035e7-0481-4028-b4bd-e91ba3634198/downloads/wwdc2026-389_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/389/4/8dd035e7-0481-4028-b4bd-e91ba3634198/downloads/wwdc2026-389_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/389/
