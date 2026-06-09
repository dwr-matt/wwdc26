# Integrate MusicKit into your app

這份技術摘要旨在為開發者總結 2026 年 WWDC 關於 **MusicKit** 整合的教學內容。本場次演示了如何將 Apple Music 功能無縫嵌入 iOS 應用程式中，並以一個健身 App 為實作案例。

---

# 技術摘要：Integrate MusicKit into your app

## 概述
本 Session 深入探討了如何透過 Swift 的 **MusicKit** 框架，讓應用程式具備播放 Apple Music 目錄及使用者個人媒體庫的能力。內容涵蓋了從權限請求、使用 `MusicPicker` 選擇音樂，到透過 `MusicPlayer` 進行播放控制，以及利用 `MusicCatalogResourceRequest` 進行進階資料獲取等完整流程。

---

## 主要概念與框架介紹

### 1. 專案配置與權限
*   **開發者 Token**：在 Apple Developer Portal 註冊 App ID 時，需勾選 MusicKit 服務以啟用自動 Token 生成。
*   **授權請求**：使用 `MusicAuthorization.request()` 非同步方法來請求使用者權限。
*   **Capabilities**：需在 Xcode 的「Signing & Capabilities」中新增「Media Library」，並填寫權限用途描述，該描述將顯示在系統授權彈窗中。

### 2. MusicKit 資料模型 (Music Items)
*   **Music Item**：是 MusicKit 的核心建構單元（如 `Song`、`Album`、`Playlist`）。
*   **屬性 (Attributes)**：包含音樂的基本資訊，如標題、內容分級。
*   **關係 (Relationships)**：描述關聯內容（如專輯包含的曲目）。
*   **關聯 (Associations)**：與類型的連結較弱（如專輯的「其他版本」）。

### 3. 音樂播放器 (Music Players)
*   **System Music Player**：控制系統音樂 App，播放會延續到 App 背景或關閉後，適合背景播放情境。
*   **Application Music Player**：僅在 App 內播放，提供完整的讀寫權限（可自訂播放佇列 Queue），需啟用 `Audio Background Mode` 才能支援背景播放。

---

## 關鍵技術與程式碼模式

### 訂閱與體驗優化
*   **Subscription Offer**：若使用者未訂閱 Apple Music，可使用 `musicSubscriptionOffer` View Modifier，在不離開 App 的情況下引導使用者訂閱。
*   **分潤機制**：透過 `MusicSubscriptionOffer.Options` 可指定 `messageIdentifier`（如 `.playMusic`）並設定合作夥伴 ID，參與 Apple Services Performance Partner Program。

### 音樂選擇器 (Music Picker)
`MusicPicker` 提供了一個原生且統一的 UI，讓使用者從目錄或個人媒體庫中選取音樂：
```swift
// 顯示 Music Picker 的 SwiftUI 實作模式
.musicPicker(isPresented: $isPickerShown, selection: $selectedSongs)
```
*   **多選支援**：將 `selection` 綁定為陣列即可支援多曲目選擇。
*   **自動篩選**：若使用者無訂閱，Picker 將自動隱藏 Apple Music 目錄，僅顯示個人媒體庫內容。

### 播放控制與 Buffer
*   **預載入**：在播放前呼叫 `player.prepareToPlay()` 可減少音訊準備時間。
*   **佇列 (Queue)**：透過 `player.queue` 管理播放內容，並可透過 `player.playbackStatus` 監聽目前播放狀態。

### 目錄請求 (Catalog Requests)
使用 `MusicCatalogResourceRequest` 獲取特定資源（如熱門歌曲推薦）：
```swift
let request = MusicCatalogResourceRequest<Song>(matching: \.id, memberOf: songIDs)
let response = try await request.response()
// 使用 MusicItemCollection 處理分頁 (Pagination) 與結果
```
*   **資源等價 (Equivalence)**：使用 `.findEquivalence` 選項，系統會自動根據地區或帳號限制（如 Explicit 内容）尋找等價資源。

---

## 開發者實用重點

1.  **狀態同步**：使用 `Observable` 物件監聽播放狀態，並將其直接整合於 SwiftUI View 中，達成 UI 與播放器同步。
2.  **效能考量**：若預知播放內容，請優先使用 `prepareToPlay()` 以提升使用者體驗。
3.  **聽歌歷史**：`effectsListeningHistory` 屬性可設定是否將播放記錄同步至系統的「最近播放」，建議依據 App 類型設定。
4.  **資源獲取**：善用 `MusicItemCollection` 的 `nextBatch()` 方法來處理大量資料的非同步載入（分頁），避免一次性載入過多資源導致 UI 卡頓。
5.  **跨平台發展**：若未來需支援 Android 或 Web，可進一步研究 Apple Music API 與 MusicKit JS。

此框架讓開發者能以極低代價，將 Apple Music 強大的音樂生態整合入任何應用程式中，提升產品的加值體驗。

<!-- resources -->

---

## 資源連結

- [Integrating MusicKit into your app](https://developer.apple.com/documentation/MusicKit/integrating-musickit-into-your-app)
- [Apple Services Performance Partner Program](https://performance-partners.apple.com/home)
- [MusicKit](https://developer.apple.com/documentation/musickit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/254/5/d4b2c60a-8a2a-41d1-a55a-0fd60d927798/downloads/wwdc2026-254_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/254/5/d4b2c60a-8a2a-41d1-a55a-0fd60d927798/downloads/wwdc2026-254_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/254/
