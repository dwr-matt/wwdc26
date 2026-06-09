# What's new in Shortcuts

這份技術摘要整理自 WWDC 2026 "What's new in Shortcuts" Session，旨在協助開發者了解如何透過最新的自動化功能、模型整合與數據儲存機制，優化 App 的快捷指令體驗。

---

# WWDC 2026 技術摘要：What's new in Shortcuts

## 1. 概述
本場 Session 介紹了 Shortcuts 的三項關鍵升級：
1.  **自動化（Automations）**：自動化設定流程全面改進，並新增三種觸發條件。
2.  **Use Model Action 增強**：整合更強大的 Apple Intelligence 模型，並引入「模型紀錄（Transcript）」除錯功能。
3.  **儲存（Storage）機制**：允許在快捷指令中持久化數據，支援跨裝置同步，讓快捷指令具備「記憶」。

---

## 2. 主要概念與 API 介紹

### 自動化新類型
快捷指令編輯器現在整合了更直覺的自動化介面，並新增以下觸發器：
*   **Screenshot Automation**：截圖儲存時觸發。
*   **Keyboard Automation**：外接鍵盤連接或斷開時觸發。
*   **Notification Automation**：收到特定 App 的通知時觸發。這是最受矚目的功能，開發者若遵循《Human Interface Guidelines》設計簡潔、資訊明確的通知，能讓使用者透過篩選關鍵字（如：標題中的「Arriving」）來建立強大的自動化。

### 升級版 Use Model Action
*   **更強大的模型**：支援最新的 Apple Intelligence 模型，並具備聯網能力獲取即時資訊。
*   **資料傳遞**：可直接將 App 的 `AppEntity` 傳遞給模型進行運算（例如從清單中篩選特定條件的物件）。

### 儲存（Storage）機制
*   **持久化儲存**：在快捷指令執行之間保留狀態。
*   **全球值（Global Values）**：支援跨多個快捷指令共享數據（例如 API Key）。
*   **跨裝置同步**：儲存的數據會透過 iCloud 自動在 iPhone、iPad 與 Mac 間同步。

---

## 3. 實作模式與技術細節

### 除錯：模型紀錄 (Transcript)
當 `Use Model` 的輸出不如預期時，開發者可進行以下除錯步驟：
1.  在 `Use Model` 動作後新增 **`Show Content`** 動作。
2.  將 `Show Content` 的輸入設為 `Use Model` 輸出的 **`Transcript`** 屬性。
3.  檢查原始資料格式：查看傳送給模型的結構化資料，確認模型是否取得了足夠的屬性（例如：發現模型無法判斷「辣度」是因為缺少「成分」屬性）。

### 資料穩定性：Entity Identifiers
為了確保資料在跨裝置同步後能正確辨識，開發者必須：
*   **使用穩定識別碼（Stable Identifier）**：不要使用會隨裝置變動的 ID，建議使用後端資料庫的 `Row ID` 或具備全域唯一性的主鍵，作為 `AppEntity` 的識別標記。

---

## 4. 開發者實用重點

1.  **優化通知設計**：
    *   確保通知內容包含明確且可解析的細節（例如：訂單 ID、狀態動詞、ETA）。
    *   好的通知設計是開啟 `Notification Automation` 的關鍵。

2.  **完善 App Intents**：
    *   透過 `App Intents` 暴露足夠的屬性給模型。例如：若希望模型能選出「辣的湯」，Entity 必須包含 `Ingredients` 陣列，而非僅有 `Name`。

3.  **運用儲存功能提升使用者體驗**：
    *   **記錄使用者偏好**：利用儲存功能讓快捷指令記住使用者過去的行為（例如：過去點過的餐點），從而避開重複建議，增加個人化感受。
    *   **簡化流程**：對於需要多次運算的任務，使用 Storage 儲存中間狀態，避免使用者重複輸入。

4.  **建議參考資源**：
    *   若要深入了解如何構建與 Use Model 互動的 App Intents，建議複習 WWDC25 的 **"Develop for Shortcuts in Spotlight with App Intents"**。

---
*編輯建議：開發者應盡快檢視現有的 App Intents 結構，確保所有關鍵數據皆已正確暴露，並透過新的 Transcript 除錯功能，測試模型是否能準確解析您的 App 業務邏輯。*

<!-- resources -->

---

## 資源連結

- [Shortcuts](https://developer.apple.com/shortcuts/)
- [Notifications](https://developer.apple.com/design/Human-Interface-Guidelines/notifications)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/310/4/50ce70ab-88da-49ff-8c57-d9136d231e76/downloads/wwdc2026-310_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/310/4/50ce70ab-88da-49ff-8c57-d9136d231e76/downloads/wwdc2026-310_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/310/
