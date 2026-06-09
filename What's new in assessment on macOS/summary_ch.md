# What's new in assessment on macOS

這是一份針對 WWDC 2026 Session「What's new in assessment on macOS」的技術摘要，專為教育科技開發者整理。

---

# WWDC 2026 技術摘要：macOS 評量應用增強 (Automatic Assessment Configuration)

## 概述
本 Session 深入探討了 macOS 上的 **Automatic Assessment Configuration (AAC) Framework** 的最新增強功能。該框架旨在協助開發者為各類教育評量或認證考試，建立一個安全、受控且防干擾的 macOS 鎖定環境（Lockdown Environment）。透過統一的 API，開發者可以精準地控制系統權限、輔助使用功能、使用者介面體驗以及運行中的程序，確保考試的公正性與安全性。

---

## 主要概念與 API 框架

要使用此框架，開發者的應用程式必須先申請並獲得 **Restricted Automatic Assessment Configuration Entitlement**。所有設定皆圍繞著 `AEAssessmentConfiguration` 物件進行，該物件定義了評量期間的所有參數與約束。

### 五大核心控制領域：
1. **系統前置條件 (System Preconditions)：** 在評量開始前，強制檢查裝置狀態。
2. **輔助使用限制 (Accessibility Restrictions)：** 針對個別學生需求，精確啟用或禁用特定輔助功能。
3. **系統體驗客製化 (System Experience Customization)：** 調整選單列、Dock、輸入法與 Finder 的互動方式。
4. **應用程式啟動限制 (Application Launch Restrictions)：** 透過白名單管理僅允許執行的程序，並阻擋自動化腳本。
5. **最佳實踐 (Best Practices)：** 確保應用程式在 macOS 更新後仍能穩定運作的建議。

---

## 技術實作與程式碼模式

開發者主要透過 `AEAssessmentConfiguration` 物件來設定各項參數。

### 1. 執行系統前置檢查
為了確保裝置處於「強化狀態 (Hardened State)」，可透過屬性檢查：
* `systemIntegrityProtectionEnabled`: 是否啟用系統完整性保護。
* `isMDMEnrolled`: 裝置是否受 MDM 管理。
* `requiredAccountType`: 設定帳戶類型（如僅限 Standard Account）。
* 禁用特定功能：`lockdownModeEnabled` 與 `iCloudPrivateRelayEnabled` 應設為 `false`。

### 2. 客製化輔助功能與輸入法
預設情況下，輔助功能會保留，但開發者可進行更細緻的控制：
* **限制輔助功能：** 若需針對特定考試限制 `Switch Control`，將其設定為不允許。
* **禁用干擾性輸入：** 設定 `dictationAllowed`、`emojiPickerAllowed`、`structuralInputAllowed` 及 `autofillAllowed` 為 `false`，防止學生透過這些功能取得作弊提示。

### 3. 選單列與 Dock 管理
* **選單列：** 可透過設定 `menuBarVisible` 控制顯示與否，並透過白名單 (`menuExtras`) 指定哪些系統選單（如 Wi-Fi、音量）可用。
* **Apple 選單：** 可進一步過濾 Apple 選單內容（例如僅顯示「睡眠」）。
* **Dock：** 透過 `allowsDock` 開啟並過濾，僅顯示考試相關的應用程式。

### 4. 檔案系統與程序隔離
* **Finder 與存檔：** 若需存取檔案，需將 Finder 加入「參與者 (Participant)」，並使用 `allowedDirectoriesAndFiles` 限定特定的存檔路徑（如 Documents 資料夾）。
* **程序監控：** 設定 `allowOnlyParticipantsToRun = true`，系統會自動終止所有未在白名單內的背景程序。
* **阻擋腳本：** 設定 `allowUserScriptExecution = false` 以禁止 Shortcuts 與 Automator 動作執行。

---

## 開發者實用重點 (Best Practices)

1. **依賴框架本身，而非自行實作：** 盡量使用 AAC 提供的 API 來鎖定系統，避免手寫複雜且易出錯的私有 API 調用。
2. **「最小權限」原則：** 預設應採取寬鬆政策，再根據評量需求逐一「嚴格限制」，避免過度限制影響考生的操作舒適度。
3. **視輔助功能為標配：** 設計應用程式時，應在開發初期就考量輔助功能，而非事後才進行補救，確保每位學生都能公平參與。
4. **善用生命週期回調 (Callbacks)：** 不要假設 `begin` 或 `end` API 呼叫的瞬間即生效，務必註冊 `AEAssessmentSession` 的過渡回調 (Transition Callbacks) 以確認會話狀態。
5. **緊跟 macOS Beta 測試：** 在每個 macOS Beta 版本發布當日，立即使用完整的考試測試矩陣 (Test Matrix) 進行驗證，並透過 Feedback Assistant 回報潛在回歸問題，避免正式版發布後影響用戶。

---
*編輯備註：透過這些 API，開發者能更有效率地建立符合教育法規與安全性要求的評量軟體，同時減輕手動維護安全環境的負擔。*

<!-- resources -->

---

## 資源連結

- [Automatic Assessment Configuration](https://developer.apple.com/documentation/AutomaticAssessmentConfiguration)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/230/4/03914f48-0bbe-4f2d-bb09-3ae676579cf2/downloads/wwdc2026-230_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/230/4/03914f48-0bbe-4f2d-bb09-3ae676579cf2/downloads/wwdc2026-230_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/230/
