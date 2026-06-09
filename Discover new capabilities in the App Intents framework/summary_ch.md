# Discover new capabilities in the App Intents framework

這是一份為開發者整理的 WWDC 2026 Session **「Discover new capabilities in the App Intents framework」** 技術摘要。

---

# Session 摘要：探索 App Intents 框架的新功能

### 概述
本 Session 由 App Intents 團隊工程師 Mo 主講，重點介紹了 2027 年發布的 App Intents 框架更新。核心目標是為開發者提供更強大的控制力、更高的靈活性，並顯著優化開發體驗。本次更新主要聚焦於三個領域：**Entity（實體）的強化**、**類型與參數的擴展**，以及**執行環境的深度控制**。

---

### 主要概念與 API 介紹

#### 1. Entity 的演進與跨 App 通訊
*   **Value Representation (值表示法)**：為了解決複雜結構類型（如地理坐標、導航目標）難以在 App 間傳遞的問題，新增了 `ValueRepresentation`。它允許開發者將自定義 Entity 映射為系統能理解的標準型別（如 `PlaceDescriptor`），讓不同 App（如從自定義 App 跳轉到地圖）能更順暢地協作。
*   **Relevant Entities (相關實體)**：透過「上下文」告訴系統特定內容何時適合出現。即便使用者未曾搜索或互動過該內容，系統也能主動將其推薦給使用者（例如：偵測到跑步活動時，推薦適合跑步的播放清單）。
*   **Entity Collection**：優化處理大量 Entity 的效能。若 Intent 只需要操作 ID 而非完整物件，使用 `EntityCollection` 可以避免系統預先解析（Resolve）所有 Entity 屬性，大幅降低開銷。
*   **Syncable Entity**：解決跨裝置一致性問題。透過實作此協定，定義「穩定 ID（Stable ID）」，確保同一實體在不同裝置（如 iPhone 與 iPad）上擁有統一識別碼，支援 Siri 的跨裝置對話與任務執行。

#### 2. 強化的類型支援
*   **原生類型支援**：現在原生支援 `Duration`（時間長度）與 `PersonNameComponents`（人名結構），系統將自動處理對應的選取器（Picker）與本地化。
*   **Union Values**：透過 `UnionValue` 巨集，允許單一參數接收多種不同的型別（例如：一個 Widget 參數同時支援「播放清單」與「地標集合」）。

#### 3. 執行環境與控制
*   **Long-Running Intent**：突破 30 秒執行上限，適合需要背景處理的長任務（如上傳大型照片）。支援透過 `Live Activity` 自動顯示進度。
*   **Cancelable Intent**：實作 `CancelableIntent` 協定並定義 `onCancel` 處理程序，讓 App 在被系統強制停止或使用者取消時，能安全地清理資源（如中斷網路請求）。
*   **Execution Targets**：明確指定 Intent 運行的進程。開發者可強制將特定 Intent 分配至 Main App 或特定的 Extension 執行，解決不同進程間存取共用資料時的潛在衝突。

---

### 程式碼模式示範

#### 使用 Value Representation 分享結構化資料
```swift
struct Landmark: AppEntity, Transferable {
    // ... 既有屬性 ...
    
    var valueRepresentations: some ValueRepresentation {
        ValueRepresentation(keyPath: \.placeDescriptor)
    }
}
```

#### 使用 Union Value 處理多種參數型別
```swift
enum Selection: UnionValue {
    case landmark(LandmarkCollection)
    case photo(PhotoAlbum)
}

struct MyIntent: AppIntent {
    @Parameter(title: "Selection")
    var selection: Selection
}
```

#### 實現長任務執行與取消處理
```swift
struct UploadIntent: LongRunningIntent, CancelableIntent {
    func perform() async throws -> some IntentResult {
        try await performBackgroundTask { progress in
            // 執行背景任務並報告進度
        }
    }
    
    func onCancel(reason: CancellationReason) {
        // 優雅處理取消邏輯
    }
}
```

---

### 開發者實用重點 (Key Takeaways)

1.  **效能最佳化**：若你的 App 處理大量資料（如照片庫、檔案清單），務必改用 `EntityCollection` 取代標準陣列，這能顯著提升系統回應速度。
2.  **整合策略**：
    *   `Spotlight`：用於讓內容可被搜尋與 Siri 檢索。
    *   `Interaction Donation`：用於讓系統學習使用習慣。
    *   `Relevant Entities`：用於主動提示系統在特定情境下顯示建議。
3.  **架構設計**：如果 App 使用了 `Widget Extension`，請善用 `Execution Targets`。若寫入權限僅限 Main App，透過此機制將 Widget 的寫入任務指向 Main App 執行，能有效解決資料競爭（Data Race）問題。
4.  **體驗提升**：對於耗時的操作，請務必導入 `LongRunningIntent`，這不僅是技術上的延長，還能透過 `Live Activity` 提供使用者透明的進度反饋，大幅提升 App 的專業感。

---
*建議參考資源：Apple 官方提供的「Travel Tracking」範例程式碼，其中包含了本次更新的完整實作展示。*

<!-- resources -->

---

## 資源連結

- [Adopting App Intents to support system experiences](https://developer.apple.com/documentation/AppIntents/adopting-app-intents-to-support-system-experiences)
- [App Intents](https://developer.apple.com/documentation/AppIntents)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/345/4/bc719e14-772a-4737-aceb-6e54cda6b511/downloads/wwdc2026-345_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/345/4/bc719e14-772a-4737-aceb-6e54cda6b511/downloads/wwdc2026-345_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/345/
