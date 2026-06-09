# Explore advanced App Intents features for Siri and Apple Intelligence

這是一份為開發者整理的 WWDC 2026 Session 技術摘要：**Explore advanced App Intents features for Siri and Apple Intelligence**。

---

# 技術摘要：探索 Siri 與 Apple Intelligence 的進階 App Intents 功能

本 Session 旨在協助開發者超越基礎的 App Intents 實作，利用 Apple Intelligence 與 Siri 的強大能力，讓 App 的互動體驗更具個人化、直覺性與深度整合。講者透過 `CosmoTunes`、`Unicorn Chat` 與 `ComicCal` 三個範例專案，展示了如何優化 Siri 的對話、提升內容的可發現性（Discoverability），以及如何透過實體註釋（Entity Annotations）將 App 內容融入系統級互動。

---

## 一、主要概念與技術框架

### 1. 塑造對話體驗 (Shaping the Conversation)
*   **自定義對話 (Custom Dialogue)：** 透過 `providesDialog` 協定，開發者可以控制 Siri 的回應文字，使之符合 App 的語氣（如：將「歌曲」稱作「曲目」）。支援完整回應（Full string）與支援性 UI 顯示文字（Supporting string）。
*   **澄清請求 (Clarifying Questions)：** 在 `perform` 方法中使用 `dialogue request`，可在執行意圖前詢問使用者缺失的參數（例如：詢問鬧鐘的名稱），提升流程的彈性。
*   **自定義視覺呈現 (Entity Display Representation)：** 定義實體的標題、副標題與圖像，讓 Siri 在展示結果時能顯示豐富的資訊，並能透過 SwiftUI 自定義視圖（Custom View Snippets）呈現更符合品牌識別的 UI。

### 2. Apple Intelligence 與行為預測 (Interaction Donations)
*   **捐贈互動 (Interaction Donations)：** 即使使用者是在 App 的 UI 內操作，也能透過 `Intent Donation Manager API` 將該行為「捐贈」給系統。這能訓練 Apple Intelligence，讓 Siri 在未來能推論並主動建議相關的操作。
*   **導航與時序整合：** 透過捐贈，Siri 可以「感知」App 當前正在進行的狀態（如：導航中、計時器運行中），並能在外部請求時提供上下文理解。

### 3. 內容可發現性 (Content Discoverability)
*   **語義索引 (Semantic Index)：** 透過 Spotlight 的語義索引，讓 Siri 能理解內容的「意義」而非僅是關鍵字。
*   **索引查詢 (Index Entity Query)：** 確保內容隨著 App 的變動即時更新（新增、修改、刪除）。
*   **意圖值查詢 (Intent Value Query)：** 當數據集過大、位於伺服器端或變動頻繁時，可透過此方式處理複雜的結構化搜尋。
*   **系統級搜尋 (System Search in App)：** 利用 `SystemSearchInApp` 結構，讓使用者可以直接透過 Siri 在你的 App 內執行搜尋。

### 4. 螢幕感知 (On-Screen Awareness)
讓 Siri 能根據「使用者目前在螢幕上看到的內容」進行互動（如：播放選單中的「第三個」項目）。
*   **NSUserActivity：** 用於標記當前螢幕的主要內容。
*   **ViewEntity 註釋：** 為螢幕上的特定項目標記識別符。
*   **集合註釋 (Collection Annotations)：** 針對大型列表，系統會「懶加載」識別符，並能處理已滾動至螢幕外的項目。
*   **自定義畫布註釋 (Custom Canvas View Annotation)：** 針對非標準的 UI 元素（如自繪圖表），告知系統這些位置對應的實體。

---

## 二、關鍵程式碼模式與實作技術

*   **實作自定義回應：**
    ```swift
    // 在 Intent 中宣告提供對話
    struct AddToPlaylistIntent: AppIntent, ProvidesDialog {
        func perform() async throws -> some IntentResult {
            // 回傳包含完整字串與支援字串的對話
            return .result(dialog: IntentDialog(full: "已加入 mixtape", supporting: "加入播放清單"))
        }
    }
    ```
*   **捐贈 UI 操作：**
    ```swift
    // 在 UI 操作完成後調用捐贈 API
    let intent = SendMessageIntent(contact: recipient, message: text)
    IntentDonationManager.shared.donate(intent)
    ```
*   **連結系統整合：**
    透過將 `AppEntityIdentifier` 賦予 `UNMutableNotificationContent` 或 `AlarmConfiguration`，系統可將通知、Now Playing 音訊與鬧鐘與 App 內部的實體緊密連結，實現「回覆這則訊息」、「播放這個版本」等跨系統指令。

---

## 三、開發者實用重點 (Takeaways)

1.  **善用權限確認 (Confirmation)：** 對於具有重大副作用（Side Effects）的操作（如：刪除、公開分享），系統會自動觸發確認。若實體是公開的，務必採用 `OwnershipProvidingEntity` 協定。
2.  **避免過度捐贈：** 僅捐贈真實的使用者行為，過多的捐贈可能會被系統忽略。
3.  **效能優化：** 若螢幕上的實體很多，請實現 `displayRepresentations` 方法，讓 Siri 只抓取純文字描述，避免頻繁存取資料庫造成延遲。
4.  **體驗一致性：** 確保你的語音回應在 AirPods 等「純語音」裝置上也能完整表達意義，不依賴視覺輔助。
5.  **由簡入繁：** 先從自定義 `Entity Display Representation` 開始，再逐步導入語義索引與螢幕感知，這樣能最快提升使用者感受到的「智慧感」。

**建議：** 開發者應下載本次的 `CosmoTunes` 等範例專案，實地操作 `App Schemas` 與螢幕感知 API 的整合，以獲得最深刻的開發體感。

<!-- resources -->

---

## 資源連結

- [App Intents Testing](https://developer.apple.com/documentation/AppIntentsTesting)
- [Donating your app’s data and actions to the system](https://developer.apple.com/documentation/AppIntents/donating-your-apps-data-and-actions-to-the-system)
- [Donations and discovery](https://developer.apple.com/documentation/AppIntents/donations-and-discovery)
- [Making app entities available in Spotlight](https://developer.apple.com/documentation/AppIntents/making-app-entities-available-in-spotlight)
- [Making actions and content discoverable by Apple Intelligence](https://developer.apple.com/documentation/AppIntents/making-actions-and-content-discoverable-by-apple-intelligence)
- [Providing contextual cues to Apple Intelligence and Siri](https://developer.apple.com/documentation/AppIntents/providing-contextual-cues-to-apple-intelligence-and-siri)
- [Apple Intelligence and Siri AI](https://developer.apple.com/documentation/AppIntents/apple-intelligence-and-siri-ai)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/343/4/00190d1d-55b6-4eb2-9ee3-e09f3d8d1c7d/downloads/wwdc2026-343_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/343/4/00190d1d-55b6-4eb2-9ee3-e09f3d8d1c7d/downloads/wwdc2026-343_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/343/
