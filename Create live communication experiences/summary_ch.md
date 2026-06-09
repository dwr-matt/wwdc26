# Create live communication experiences

這是一份關於 WWDC 2026 Session「Create live communication experiences」的技術摘要，專為需要整合即時通訊功能的開發者所整理。

---

# 技術摘要：建立即時通訊體驗 (Create live communication experiences)

## 概述
本 Session 介紹了 Apple 推出的全新 **Live Communication Kit**，旨在協助開發者提供豐富、原生的通訊介面。透過此框架，通訊應用程式可以無縫整合至系統級體驗，包括鎖定畫面（Lock Screen）、動態島（Dynamic Island）、電話 App 的「最近通話（Recents）」列表以及聯絡人詳情頁面，讓使用者的通訊體驗更直覺、更統一。

---

## 主要概念與 API 架構

### 1. 核心物件：Conversation
「對話 (Conversation)」是整個架構的核心，代表兩人或多人之間的即時互動。
*   **Handle (識別碼)**：定義對話參與者的身份，包含 `kind` (類型)、`value` (識別值) 與 `display name` (顯示名稱)。正確設定 `kind` 可讓系統自動匹配聯絡人照片與名稱。
*   **Capabilities (能力)**：描述對話支援的功能（如：靜音、暫停、影像通話、合併通話）。系統會依據這些能力動態調整 UI 控制項。

### 2. 架構模式
*   **Conversation Manager**：應用程式與系統溝通的橋樑，負責報告對話狀態、發起對話及處理系統事件。
*   **Delegate 模式**：應用程式必須實作 `ConversationManagerDelegate`，處理所有來自系統的操作指令（如：接聽、掛斷、合併通話）。此模式確保系統 UI 與 App 內部狀態高度一致，避免狀態不對稱。

### 3. 對話生命週期
對話從報告給系統開始，經歷以下狀態轉換：
1.  **Idle**：收到來電，裝置開始響鈴。
2.  **Joining**：使用者點擊接聽，App 進行連線準備。
3.  **Joined**：連線完成，開始傳輸影音資料。
4.  **Leaving**：結束通話。
5.  **Left**：徹底清理資源與連線。

---

## 程式碼模式與實作技術

### 配置 Conversation Manager
在 App 啟動時建立 Manager 並設定相關組態：
```swift
let configuration = ConversationConfiguration(
    ringtone: "myRingtone.mp3",
    icon: myAppIcon,
    supportsVideo: true,
    recentsSupport: true
)
let manager = ConversationManager(configuration: configuration)
manager.delegate = self
```

### 處理來電 (PushKit + Reporting)
當伺服器發送 VoIP 推播時，使用 PushKit 喚醒 App 並立即向系統回報：
```swift
func pushRegistry(_ registry: PKPushRegistry, didReceiveIncomingPushWith payload: [AnyHashable : Any]) {
    let handle = extractHandle(from: payload)
    let update = ConversationUpdate(handle: handle, capabilities: [.video, .pause, .merge])
    manager.report(update, for: conversationUUID)
}
```

### 處理動作 (Perform Action)
無論是從鎖定畫面還是 App 內部發起的動作，皆會匯集至同一個 Delegate 方法處理：
```swift
func conversationManager(_ manager: ConversationManager, perform action: ConversationAction) {
    switch action {
    case .join(let conversation):
        // 啟動影音連線工作
        startMediaStream(for: conversation)
    case .end(let conversation):
        // 釋放資源
        teardownMedia(for: conversation)
    case .merge(let conversationA, let conversationB):
        // 合併兩通對話的媒體流
        mergeStreams(a: conversationA, b: conversationB)
    }
}
```

---

## 開發者實用重點 (Key Takeaways)

1.  **全面替換舊 API**：若您的應用程式目前使用 `CXProvider` (CallKit)，建議儘速遷移至 **Live Communication Kit**，以獲得更靈活且功能豐富的原生介面支援。
2.  **善用 App Intents**：為了讓「最近通話」與「Spotlight」支援重新撥號 (Redial)，請務必實作並捐贈 (Donate) `App Intents`，確保系統知道如何再次啟動您的通訊服務。
3.  **明確區分群組成員**：在群組對話中，需區分 `members`（受邀者）與 `activeRemoteMembers`（正在傳輸影音的參與者），這對系統呈現正確的狀態 UI 至關重要。
4.  **處理背景模式**：別忘了在 Xcode 的 Capabilities 中開啟「Audio & Voice over IP」背景模式，確保 App 在鎖定狀態下仍能持續處理通訊流。
5.  **單一狀態來源**：堅持透過 `ConversationManager` 進行狀態更新，避免在 UI 層手動管理對話狀態，確保系統與 App 邏輯保持同步。

---
*技術編輯註：Live Communication Kit 極大簡化了通訊 App 的系統整合成本，建議開發者優先確認自家 App 的媒體串流處理與此架構的對接點。*

<!-- resources -->

---

## 資源連結

- [Initiating VoIP conversations with LiveCommunicationKit](https://developer.apple.com/documentation/LiveCommunicationKit/initiating-voip-conversations-with-livecommunicationkit)
- [Responding to VoIP Notifications from PushKit](https://developer.apple.com/documentation/PushKit/responding-to-voip-notifications-from-pushkit)
- [LiveCommunicationKit](https://developer.apple.com/documentation/LiveCommunicationKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/226/4/f8343d5b-0c78-4396-be05-956666fb4ae0/downloads/wwdc2026-226_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/226/4/f8343d5b-0c78-4396-be05-956666fb4ae0/downloads/wwdc2026-226_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/226/
