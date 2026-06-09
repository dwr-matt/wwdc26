# Build intelligent Siri experiences with App Schemas

這是一份關於 WWDC 2026 Session「Build intelligent Siri experiences with App Schemas」的技術摘要，協助開發者快速掌握將應用程式整合至 Apple Intelligence 的核心路徑。

---

# 技術摘要：利用 App Schemas 構建智慧 Siri 體驗

## 1. 概述
本場次深入探討如何透過 **App Intents** 框架，將應用程式的功能與內容深度整合至 Siri 與 Apple Intelligence 中。重點在於將傳統的「App 內操作」擴展為「系統級的智慧互動」。透過 **App Entities** 與 **App Schemas**，開發者能讓 Siri 理解應用程式內的內容結構，實現更自然的語意查詢、跨 App 的內容傳遞，以及更強大的場景感測（On-screen context）。

---

## 2. 主要概念與框架介紹

### A. App Entities (應用程式實體)
App Entities 是內容的結構化表示，是 Siri 理解應用程式資料的基礎。
*   **用途**：定義應用程式中的「名詞」（如訊息、聯絡人、會議）。
*   **關鍵屬性**：需定義實體是什麼、如何識別，以及哪些屬性（如標題、日期）對系統而言是有意義的。

### B. App Schemas (應用程式架構)
Schema 是對 App Intents 的進階定義，專為 Siri 設計。
*   **用途**：讓 Siri 了解特定領域的行為與意圖。
*   **App Schema Domains**：將相關的 Schemas 打包（例如：訊息、照片、郵件領域）。當 App 採用某個 Domain 時，Siri 便能流暢地處理該領域的自然語言指令，無需開發者手動解析語意。

### C. 語意查詢與索引 (Entity Resolution)
*   **Indexed Entity**：將實體加入系統的語意索引（Semantic Index）。這不僅是字串比對，更支援依據「意義」搜尋（例如：「搜尋關於電影的訊息」）。
*   **Entity String Query**：適用於資料量過大、存在伺服器端或變動頻繁的場景，由開發者控制查詢邏輯。

### D. 內容傳遞與整合
*   **On-screen Awareness**：透過 View Annotations 將螢幕上顯示的元件與 App Entity 連結，讓使用者能說出「總結這則訊息」這類指令。
*   **Transferable**：讓實體可以跨 App 傳輸（如：將此人的聯絡資訊透過郵件傳送）。

---

## 3. 關鍵技術實作模式

### 實作步驟流程：
1.  **建模 (Modeling)**：使用 `AppEntity` 定義應用內容。
2.  **定義索引 (Indexing)**：實作 `IndexedEntity`，並指定關鍵屬性供 Spotlight 進行語意分析。
3.  **採用 Schema (Adoption)**：在 Xcode 中選擇適當的 `App Schema`（如 `SendMessageIntent`）。
4.  **處理流程 (Mapping)**：
    *   接收系統傳入的參數。
    *   執行業務邏輯（如發送訊息）。
    *   將結果以 `AppEntity` 形式返回給系統。

### 程式碼與工具提示：
*   **自動化提示**：Xcode 會檢查 Schema 的完整性。例如，若實作了 `SendMessage`，Xcode 會提示你同時實作 `DraftMessage` 以確保完整的 Siri 體驗，並提供「Fix-it」自動產生樣板程式碼。
*   **Main Actor 隔離**：若動作涉及 UI 更新（如呼叫訊息創建畫面），務必在 `MainActor` 中執行。

---

## 4. 開發者實用重點 (Best Practices)

1.  **測試優先 (Test Early and Often)**：
    *   **App Intents Testing**：在不觸發 Siri 的情況下，針對 Intent 進行單元測試，驗證業務邏輯。
    *   **Shortcuts App**：用於驗證 Intent 的參數顯示與使用者體驗。
    *   **Spotlight**：驗證內容是否被正確索引。
    *   **End-to-End**：最後在真實 Siri 環境中測試自然語言處理與跨 App 流程。

2.  **避免重複造輪子**：優先使用 Apple 提供的 `App Schema Domains`，它們已經定義好完整的合約與溝通方式，能大幅減少語意解析的開發工作。

3.  **內容傳遞策略**：
    *   若是現有資料：使用 `IntentValueQuery` 進行匹配。
    *   若是全新資料：使用 `Importing` 處理 `Transferable`，轉化為 App 內的新實體。

4.  **體驗優化**：透過正確標註螢幕上的視圖（View Annotations），能讓使用者體驗從「打開 App」轉向「在當前螢幕上直接操作」，這是提升使用者黏著度的關鍵。

---

**總結建議**：請從定義核心資料作為 `App Entity` 開始，並選擇一個適合您 App 領域的 `App Schema Domain` 進行實作。透過 Xcode 提供的整合輔助工具，您將能以最少的工作量，最大化您的 App 在 Apple Intelligence 體系中的曝光度與易用性。

<!-- resources -->

---

## 資源連結

- [Integrating your messaging app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-messaging-app-with-apple-intelligence)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [Messages](https://developer.apple.com/documentation/AppIntents/app-schema-domain-messages)
- [App schema domains](https://developer.apple.com/documentation/AppIntents/app-schema-domains)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/240/4/d46aac11-3990-42cd-bb33-4ce5e958b902/downloads/wwdc2026-240_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/240/4/d46aac11-3990-42cd-bb33-4ce5e958b902/downloads/wwdc2026-240_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/240/
