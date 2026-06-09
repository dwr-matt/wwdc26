# Code-along: Add persistence with SwiftData

這份技術摘要旨在幫助開發者快速掌握 WWDC 2026 Session「Code-along: Add persistence with SwiftData」的核心內容。本場次以 Wishlist 範例應用為基礎，展示如何將現有基於記憶體的 SwiftUI 應用程式轉型為具備資料持久化（Persistence）能力的現代化架構。

---

### 1. 概述：從記憶體資料轉向 SwiftData
本 Session 的核心目標是指導開發者將原本依賴記憶體（RAM）儲存、重啟後資料即遺失的 SwiftUI 應用，透過 **SwiftData** 重構為具備持久化能力的 App。講師演示了如何定義 Schema、處理模型間的關聯（Relationship）、利用 `ModelContext` 取代自定義的資料來源（Data Source），以及如何優化查詢效能。

### 2. 主要概念與技術框架
*   **SwiftData Model Macro**：取代原有的 `@Observable`，透過 `@Model` 宏自動生成資料持久化所需的屬性與觀察機制。
*   **Schema 設計**：定義應用所需的資料結構，包含類別（Class）與屬性。
*   **ModelContext 與 Container**：負責資料的插入、更新、刪除與自動儲存。透過 `@Query` 宏在 SwiftUI 視圖中獲取資料。
*   **模型繼承（Model Inheritance）**：利用類別繼承來處理結構相似但具備特定差異的資料模型（如 `TripGoal` 與 `ActivityGoal` 繼承自 `Goal`）。
*   **外部檔案參考**：針對大型二進位資料（如圖片），建議使用模型儲存路徑或額外的圖片模型，而非直接將全解析度影像存入資料庫，以保持資料庫運作效能。

### 3. 關鍵程式碼模式與遷移技術

#### A. 模型轉換
將傳統物件模型轉為 SwiftData 模型：
```swift
// 修改前
@Observable class Activity { ... }

// 修改後
@Model class Activity {
    var name: String
    var isComplete: Bool
    var dateEdited: Date
    // ...
}
```

#### B. 處理「一對多」關聯
使用 `@Relationship` 來定義資料間的關聯性，並設定刪除規則（Cascade Delete）：
```swift
@Model class Trip {
    @Relationship(deleteRule: .cascade) 
    var activities: [Activity] = []
}
```

#### C. 使用 `@Query` 進行高效篩選
避免全量讀取後在記憶體中過濾，改用 `Predicate` 讓資料庫層級完成查詢：
```swift
// 在視圖中宣告查詢
@Query(filter: #Predicate<Trip> { trip in 
    trip.collection == selectedCollection 
}, sort: \.name) var trips: [Trip]
```

#### D. 即時更新屬性（Continuous Observation）
由於 SwiftData 模型移除了 `didSet` 屬性觀察器，建議使用觀察框架的新功能：
```swift
// 在視圖初始化階段設定
withContinuousObservation {
    // 監控模型變化並更新 dateEdited
}
```

### 4. 開發者實用重點與優化策略

1.  **記憶體與 IO 的平衡**：
    *   **不要**將所有資料一次性載入記憶體。
    *   **應該**使用帶有 `Predicate` 的 `@Query`，僅請求需要的資料子集，如同向圖書館員指定書名而非搬走整座圖書館。
2.  **效能最佳化**：
    *   圖片應儲存縮圖（Thumbnail）於資料庫，全解析度檔案則使用檔案系統參考（External File Reference）。
    *   移除過時的 `Data Source` 類別，直接利用 SwiftData 內建的持久化機制，可大幅減少維護程式碼量。
3.  **錯誤處理與使用者體驗**：
    *   在處理持久化操作（如更新進度）時，務必捕捉可能發生的磁碟空間不足或查詢錯誤。
    *   利用 `.contentUnavailableView` 來優化無搜尋結果時的 UI 反饋。
4.  **開發流程建議**：
    *   先定義 Schema 與關係。
    *   將視圖層的資料讀取方式從 `Environment` 注入轉為使用 `@Query`。
    *   最後針對 UI 互動（如排序、狀態同步）補上遺漏的邏輯。

---
*註：Session 最後提到 2027 年的觀察框架（Observation Framework）增強功能，這是 SwiftData 在未來版本中保持 UI 與資料同步的重要技術方向。*

<!-- resources -->

---

## 資源連結

- [Wishlist: Planning travel in a SwiftUI app](https://developer.apple.com/documentation/SwiftUI/wishlist-planning-travel-in-a-swiftui-app)
- [SwiftData](https://developer.apple.com/documentation/SwiftData)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/275/4/7c64f887-3c3c-4bdf-8472-72d6b96f8e3d/downloads/wwdc2026-275_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/275/4/7c64f887-3c3c-4bdf-8472-72d6b96f8e3d/downloads/wwdc2026-275_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/275/
