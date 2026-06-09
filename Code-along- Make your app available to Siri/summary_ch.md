# Code-along: Make your app available to Siri

這份摘要旨在幫助開發者快速掌握 WWDC 2026 Session「**Code-along: Make your app available to Siri**」的核心內容與實作技術。

---

### 概述：讓您的 App 具備 Siri 智慧
本 Session 以「ComicCal」（一款 SwiftUI 行事曆 App）為範例，逐步展示如何透過 **App Intents 框架** 與 **App Schemas**，讓 App 的內容與動作能被 Siri 與 Apple Intelligence 理解並操作。開發者無需自行撰寫自然語言處理（NLP）邏輯，只需透過標準化的架構，即可讓 App 支援查詢、更新、新增及刪除等功能，並具備與螢幕畫面連動的「On-screen Awareness」能力。

---

### 主要概念與框架介紹

1.  **App Intents 框架**：開發者將 App 功能暴露給系統的核心，讓 Siri 和其他系統體驗能存取 App 的內容（Entities）與動作（Intents）。
2.  **App Schemas**：預定義好的結構模組（例如 Calendar Domain），定義了實體（Entity）屬性、動作參數與輸出。Siri 已具備這些 schema 的知識，無需開發者額外訓練模型。
3.  **App Entities**：代表 App 內的物件（如 Event、Calendar、Attendee）。
    *   `IndexedEntity`：支援 Spotlight 索引，具備語意理解能力，可透過名稱、屬性或上下文檢索。
    *   `TransientAppEntity`：代表臨時性的實體（如活動參與者），不需要唯一 ID 或獨立檢索，適用於附屬於其他主體的資料。
4.  **On-screen Awareness (螢幕感知)**：透過 SwiftUI View Modifier，讓 Siri 知道目前使用者畫面上的具體物件，實現如「Email 這個活動的所有人」等指令。

---

### 實作技術與程式碼模式

#### 1. 定義實體 (Entities)
使用 Xcode 的自動補全功能（例如輸入 `calendar_`），選取對應的 Schema snippet：
*   **Conforming to `IndexedEntity`**：需配置 `displayRepresentation`（標題與圖示）與 `EntityQuery`（實作 `allEntities` 與根據 ID 查找的方法）。
*   **資料捐贈 (Donation)**：使用 `CSSearchableIndex` 在資料增刪改時手動更新索引（`indexAppEntities` / `deleteAppEntities`），這是確保 Spotlight 與 Siri 搜尋準確的關鍵。

#### 2. 使用 Dependency Injection
透過 `@Dependency` 屬性包裝器（Property Wrapper）注入共用資源（如 `CalendarManager`），確保 Intent 與 Query 能存取同一個資料層實體，避免重複實例化。

#### 3. 處理更新 Intent 的選擇性參數
更新 Intent 參數可能為 `nil`（清除資料）或 `unset`（不變更）。透過 `IntentParameter` 的 `value` 狀態進行判斷：
*   **Set (with value)**: 執行更新。
*   **Set (nil)**: 執行清除動作。
*   **Unset**: 忽略此參數。

#### 4. 自訂 Siri 結果卡片 (Snippet Views)
若不滿意系統預設的卡片樣式，可實作：
*   建立 SwiftUI View。
*   在 Intent 的 `perform` 方法回傳型別中加入 `ShowSnippetView`，並將自訂 View 傳入，打造 App 專屬的視覺風格。

---

### 開發者實用重點 (Key Takeaways)

*   **無須自然語言訓練**：完全採用 Schema 與 Entity 定義，系統會自動處理語意解析與詢問確認流程。
*   **善用 Snippets**：Xcode 內建的 `calendar_` 等前綴詞是建立 Entities 與 Intents 的最佳捷徑，確保架構符合標準。
*   **精簡的畫面感知**：僅需兩個 Modifier：
    *   `AppEntityIdentifier` (對 List 項目)。
    *   `UserActivity` (對 Detail 視角)。
    *   即可讓 Siri 對「目前的螢幕狀態」進行指令處理。
*   **Open Intent 的必要性**：若希望使用者從 Siri 點擊搜尋結果能正確跳轉至特定畫面（例如特定的行事曆事件），必須實作一個符合 `OpenSchema` 的 Intent。
*   **測試資源**：建議搭配官方發佈的「App Intents Testing Framework」進行自動化測試，以驗證 Siri 的操作邏輯。

---

**推薦參考影片：**
*   *Build Intelligent Siri Experiences with App Schemas*（深入了解架構概念）
*   *Get to Know App Intents*（適合剛接觸框架的開發者）

<!-- resources -->

---

## 資源連結

- [Integrating your calendar app with Apple Intelligence](https://developer.apple.com/documentation/AppIntents/integrating-your-calendar-app-with-apple-intelligence)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Donations and discovery](https://developer.apple.com/documentation/AppIntents/donations-and-discovery)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [Calendar](https://developer.apple.com/documentation/AppIntents/app-schema-domain-calendar)
- [App schema domains](https://developer.apple.com/documentation/AppIntents/app-schema-domains)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/344/4/ee45cb19-e252-41f4-a2e0-e9b59238c7aa/downloads/wwdc2026-344_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/344/4/ee45cb19-e252-41f4-a2e0-e9b59238c7aa/downloads/wwdc2026-344_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/344/
