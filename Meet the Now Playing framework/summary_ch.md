# Meet the Now Playing framework

這是一份為您整理的 WWDC 2026 Session「Meet the Now Playing framework」技術摘要。

---

# 技術摘要：Meet the Now Playing Framework

## 概述
本 Session 介紹了 Apple 新推出的 **Now Playing Framework**，旨在簡化開發者將媒體內容整合至系統級體驗（如鎖定畫面、控制中心、動態島、StandBy 及 CarPlay）的流程。無論是本地播放還是遠端遙控裝置，該框架都提供了統一且標準化的 API，讓媒體控制不僅限於 App 內部，而是延伸至整個 Apple 生態系統。

---

## 主要概念與 API 架構

### 1. Media Sessions API (本地播放)
用於將 App 內播放的內容呈現於系統介面。核心概念是透過「宣告式」的方式，將 App 的播放狀態與系統同步。

*   **`MediaSessionRepresentable` Protocol**: 定義了 App 與系統間的「合約」。開發者需定義內容標題、描述、媒體類型（Audio/Video）、持續時間及 Artwork。
*   **`MediaSession`**: 連接 `Representable` 與系統的橋樑。當其實例化並綁定播放模型後，系統會自動監控模型的變化並更新 UI。

### 2. Remote Media Sessions API (遠端播放)
專為遙控外部裝置（如智慧音箱、TV）設計，利用 App Extension 與 Push Notifications (APNs) 來實現跨裝置的狀態更新與控制。

*   **運作流程**: 
    1. 遠端裝置狀態變更時發送通知至伺服器。
    2. 伺服器透過 APNs 推送更新至 iPhone。
    3. 系統啟動 **App Extension** 處理更新後的狀態，並反映於系統介面。

### 3. Media Sharing Extensions
這是一個簡化跨裝置串流的技術，讓 App 無需自行埋入各種品牌的硬體 SDK，即可使用系統統一的「媒體裝置選擇器」進行播放控制，大幅降低維護成本。

---

## 程式碼模式與技術實作

### 實作 Local Media Session
開發者需讓播放模型 (Model) 符合 `MediaSessionRepresentable`，並實作必要的屬性：

```swift
// 範例：定義內容描述
let content = GenericContent(
    identifier: soundID,
    title: soundName,
    subtitle: description,
    type: .audio,
    duration: .continuous
)

// 定義播放指令 (Commands)
// 使用閉包處理系統傳來的互動指令
let commands = Commands(
    play: { player.resume() },
    pause: { player.pause() },
    next: { player.skipToNext() }
)
```

### 實作 Remote Media Session Extension
使用 `RemoteMediaSessionExtension` 來橋接遠端硬體：

1.  **設定**: 在擴充功能中設定 `RemoteMediaExtensionPointIdentifier`。
2.  **狀態更新**: 當接收到來自伺服器的狀態（Payload）時，更新 `Observable` 模型，系統將自動同步遠端裝置的 UI。
3.  **裝置與音量**: `Devices` 屬性用於告知系統目前遠端裝置的狀態，並透過閉包處理系統級的音量調整請求。

---

## 開發者實用重點

1.  **減少重複開發**: 透過 `Now Playing` 框架，開發者不再需要自行編寫繁瑣的 `MPNowPlayingInfoCenter` 設定，大幅簡化邏輯。
2.  **響應式更新**: 框架設計高度依賴 `@Observable` 模型。只要模型的屬性更新，系統 UI 即可自動反應，無需手動推送狀態。
3.  **提升 UX 一致性**: 採用此框架後，App 內容在所有系統介面（包括 Vision Pro、Apple TV、Apple Watch）的表現將高度一致，且具備系統級的穩定性。
4.  **未來擴充性**: 利用 `Media Sharing Extensions`，當未來 Apple 支援更多媒體通訊協定時，App 可直接獲得支援而無須更新 SDK，將開發重點回歸於「媒體內容」本身。

---

**建議延伸閱讀資源：**
*   *Publishing Media Sessions on Apple Developer Documentation*
*   *Setting Up a Remote Notification Server*
*   *Routing media to third-party devices*

<!-- resources -->

---

## 資源連結

- [Routing media to third-party devices](https://developer.apple.com/documentation/AVSystemRouting/routing-media-to-third-party-devices)
- [Publishing remote media sessions](https://developer.apple.com/documentation/NowPlaying/publishing-remote-media-sessions)
- [Publishing media sessions](https://developer.apple.com/documentation/NowPlaying/publishing-media-sessions)
- [Setting up a remote notification server](https://developer.apple.com/documentation/UserNotifications/setting-up-a-remote-notification-server)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/312/5/3f128d25-f1c6-49d3-a9c0-0bdc22af5f95/downloads/wwdc2026-312_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/312/5/3f128d25-f1c6-49d3-a9c0-0bdc22af5f95/downloads/wwdc2026-312_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/312/
