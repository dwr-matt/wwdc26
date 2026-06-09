# Secure your apps with App Attest

這是一份為開發者整理的 WWDC 2026 Session「Secure your apps with App Attest」技術摘要。

---

# 技術摘要：使用 App Attest 保護您的應用程式

本 Session 由 Apple Trust and Safety 團隊工程師 Manthan 主講，重點介紹如何利用 **App Attest** 機制來確保應用程式的完整性，防止應用程式遭竄改（如注入作弊功能、偽造 API 請求）或在未授權環境下執行。此技術透過 Apple 硬體底層的 Secure Enclave 提供加密證明，並引入了新的風險偵測指標。

---

## 1. 主要概念與核心流程

App Attest 的核心目標是確保伺服器接收到的請求來自「運行於真實 Apple 硬體上的合法應用程式」。其運作流程分為三大核心階段：

### A. 產生金鑰 (Key Generation)
*   **安全隔離區 (Secure Enclave)：** App Attest 會在裝置的 Secure Enclave 中建立金鑰對，私鑰無法被取出。
*   **Key ID：** 系統返回公鑰雜湊值作為 Key ID，開發者需將其存入 Keychain。
*   **最佳實踐：** 建議為每個使用者產生一組金鑰；金鑰綁定裝置，且在 App 重灌或裝置還原時會失效。

### B. 證明 (Attestation)
*   **驗證硬體與簽名：** 應用程式透過 Apple 伺服器取得證明物件 (Attestation Object)。伺服器收到後，需驗證其憑證鏈 (Certificate Chain) 與接收到的挑戰碼 (Challenge)。
*   **硬體安全性檢查：** 證明物件包含該裝置開機後的硬體屬性快照。
*   **新增擴充功能 (Extensions)：** iOS 27+ 引入了 `launch validation category`（如 App Store 或 TestFlight 下載）與 `bundle version`，讓伺服器能更精確識別非法竄改。

### C. 斷言 (Assertions)
*   **保護傳輸負載：** 當 App 發送敏感請求時，使用先前產生的金鑰進行簽名（Assertion）。
*   **反重放攻擊：** 斷言中包含一個 **Counter（計數器）**，伺服器必須追蹤該值並確保其嚴格遞增，以防止重放攻擊。

---

## 2. 關鍵技術亮點

*   **跨平台支援：** App Attest 現已支援 macOS 27+，並針對 macOS 強制要求「完整安全模式 (Full Security Mode)」與「系統完整性保護 (SIP)」。
*   **Key Access Control Property：** 透過 ACL blob OID，伺服器可以確認裝置是否關閉了安全防護（如嘗試越獄或關閉 SIP）。
*   **風險指標 (Fraud Metric)：** 提供一項強大的數據，計算特定裝置在過去 30 天內產生的唯一已證明金鑰數量。若該指標異常飆升，可能代表該裝置正被用於大規模非法請求（如作為偽造請求的代理伺服器）。

---

## 3. 開發者實用建議 (Best Practices)

### 伺服器端職責
*   **必須由伺服器控制流程：** 不要讓用戶端決定何時進行證明，以防止請求超載（Rate Limiting）。
*   **嚴謹的驗證：** 永遠在伺服器端驗證所有證明物件，絕對不要相信用戶端的自我回報。
*   **實作指數退避 (Exponential Backoff)：** 若發生證明失敗，應在 App 中實作退避機制，避免對 Apple 的證明伺服器發動拒絕服務攻擊。

### 錯誤處理與使用者體驗
*   **Graceful Degradation（優雅降級）：** 當 App Attest 無法支援或證明失敗時，**不要立即封鎖使用者**。應採取降級策略（例如限制部分功能、加強後台風險監控），除非風險評估確認其為惡意用戶。
*   **避免盲目撤銷：** 重灌或系統更新可能導致金鑰輪替，不要因為出現新金鑰就直接拒絕舊使用者。

---

## 4. 總結流程與檢查清單

1.  **整合 API：** 在 App 中使用 `IsSupported` API 檢查裝置環境，將此訊號作為風險評估的一部分。
2.  **背景任務處理：** 在非使用者操作流程的背景任務中執行證明 (Attestation)。
3.  **監控與分析：** 
    *   監控 `launch validation category` 和 `bundle version` 的變更。
    *   追蹤 Assertion Counter 是否遞增。
    *   利用 Fraud Metric 建立風險基準，並將其作為反詐騙管道的訊號。
4.  **定期更新：** 確保您的應用程式使用最新的 SDK 編譯，以獲取 iOS 27/macOS 27 之後的最新安全特性。

---

**編輯觀點：** 
App Attest 已從單純的「應用程式驗證工具」演變為一套完整的「信任評估框架」。對於處理敏感數據（如金融交易、權限驗證、公平競爭機制）的 App 開發者，結合 **Fraud Metric** 進行多維度風險評估，將是未來建構 App 安全體系的標準配置。

<!-- resources -->

---

## 資源連結

- [W3C Authenticator Data](https://www.w3.org/TR/webauthn-3/#sctn-authenticator-data)
- [About System Integrity Protection on your Mac](https://support.apple.com/en-us/102149)
- [DeviceCheck](https://developer.apple.com/documentation/DeviceCheck)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/201/4/d3eb2e5b-5104-4aee-a754-9985008a5b06/downloads/wwdc2026-201_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/201/4/d3eb2e5b-5104-4aee-a754-9985008a5b06/downloads/wwdc2026-201_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/201/
