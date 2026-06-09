# Extend Reality Composer Pro 3 functionality with Xcode

這是一份關於 Apple WWDC 2026 Session「Extend Reality Composer Pro 3 functionality with Xcode」的技術摘要。

---

# 擴充 Reality Composer Pro 3 功能 (使用 Xcode 外掛)

## 概述
本 Session 介紹了如何透過 Xcode 為 **Reality Composer Pro 3 (RCP 3)** 開發自定義外掛 (Plugins)。這項強大的功能允許開發者將專案中的自定義邏輯、資料結構與視覺化元素直接整合至 RCP 3 編輯器中。透過此機制，藝術家與設計師無需重新編譯應用程式，即可在編輯器內即時預覽並操作專案專屬的組件（Components）、系統（Systems）、動畫序列（Sequencer Actions）與腳本節點（Script Graph Nodes）。

---

## 主要概念與框架

### 1. 專案架構與協作機制
*   **整合開發環境**：RCP 專案與 Xcode 專案存放在同一個 Git 儲存庫中。
*   **雙重 Schema 設定**：Xcode 專案包含兩個主要 Target（Schema）：
    *   **App Target**：負責最終遊戲或應用程式的構建。
    *   **Plugin Target**：負責編譯為動態函式庫（Dynamic Library），供 RCP 載入。
*   **同步與預覽**：透過 Simulation Bar 中的「Run with Xcode」功能，實現 RCP 與 Xcode 的即時連結，讓編輯器能直接調用開發中的程式碼。

### 2. RCP Plugin 框架
*   **Plugin Protocol**：開發者需實作 `RealityComposerProPlugin` 協議，並定義 `createRealityComposerProPlugin()` 函數（需導出為 C 語言介面），以便編輯器能正確載入外掛。
*   **註冊機制**：在 Plugin 的 `setup` 方法中，使用傳入的 `context` 註冊自定義組件、系統及腳本模組。

---

## 關鍵程式碼模式與技術

### 自定義組件與系統 (Components & Systems)
為了讓 RCP 識別自定義組件，該結構必須遵循 `Component` 並實作 `Codable`。
```swift
struct CauldronComponent: Component, Codable {
    var waterLevel: Float = 0.0
    var rotationSpeed: Float = 0.0
}
```

### 自定義動畫 (Entity Action)
透過實作 `EntityAction` 協定，開發者可以定義自定義動畫節點，並在 `Sequencer` 中使用。
*   **Protocol**：需繼承 `EntityAction` 與 `Codable`。
*   **註冊與執行**：使用 `subscribe` 方法監聽 RealityKit 的更新事件，並在動畫執行期間計算內插值 (Interpolation)。

### 腳本節點擴充 (Script Graph Nodes)
利用 `@Scriptable` 巨集，可以極快地將組件屬性暴露給 Script Graph。
1.  **Macro 標註**：在組件 Struct 上使用 `@Scriptable`。
2.  **模組註冊**：在 Plugin 的 `setup` 函數中，使用 `RealityKit.Scripting` 配置，將生成的 Schema 註冊到編輯器中。

---

## 開發者的實用重點

1.  **縮短迭代週期**：
    *   藝術家可直接在編輯器內透過 Inspector 調整自定義組件的屬性（如：水位、旋轉速度）。
    *   透過插件，系統變更會「即時」在編輯器內反映，省去頻繁 Build/Deploy 的時間。

2.  **除錯技巧**：
    *   開發者可以在 Xcode 中對 plugin 程式碼設置中斷點（Breakpoint）。
    *   透過「Attach to Process」連結至 RCP 編輯器，即可直接在 Xcode 中偵錯正在運作的編輯器邏輯。

3.  **效能考量與維持**：
    *   雖然 Script Graph 適合快速原型，但當邏輯過於複雜（如涉及複雜的水體計算或與 SwiftUI 互動）時，轉移至 Swift 程式碼會更易於維護。

4.  **發佈與安全性**：
    *   RCP 3 會針對外掛載入進行「信任 (Trust)」確認，以確保專案安全性。
    *   開發者需注意，在修改 plugin 後，通常需要重新啟動 RCP 編輯器以載入最新的 Library 變更。

---

## 建議深入研究的 Session
*   **Iterate your spatial scenes faster with Reality Composer Pro 3**：探討 RCP 3 的核心工作流。
*   **Design No-Code Games with Reality Composer Pro 3**：深入了解 Script Graph 的視覺化腳本設計。
*   **Explore Advances in RealityKit**：掌握 RealityKit 最新的底層 API 更新。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/281/6/1aef704f-ccc6-4c1d-b7b7-94da42d29609/downloads/wwdc2026-281_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/281/6/1aef704f-ccc6-4c1d-b7b7-94da42d29609/downloads/wwdc2026-281_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/281/
