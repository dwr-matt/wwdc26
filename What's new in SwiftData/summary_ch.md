# What's new in SwiftData

這是一份為開發者準備的 **WWDC 2026：What's new in SwiftData** 技術摘要。

---

# WWDC 2026 技術摘要：SwiftData 的新功能

本場次由工程師 Thomas 主講，介紹了 Apple 2027 年度發布中 SwiftData 的重大更新。本次更新的核心目標在於提升 SwiftData 在不同架構層級的靈活性與觀察能力，特別是在 UI 分組顯示、非 SwiftUI 環境的資料觀察，以及資料變更歷史追蹤方面的強化。

## 主要概念與 API 介紹

### 1. 查詢結果分組 (Sectioning)
SwiftData 現在原生支援 `Query` 的分組功能，讓開發者無需手動處理陣列邏輯，即可在 SwiftUI 中直接以分區塊（Section）的形式呈現資料。

*   **關鍵 API**：`@Query` 的 `sectionBy` 參數。
*   **功能**：透過傳入 KeyPath，SwiftData 會自動根據該屬性將資料集分組，並透過 `.sections` 屬性提供分組後的結構。

### 2. Codable 屬性支援
過去 SwiftData 難以直接儲存來自第三方框架（如 MapKit）的類別型物件，因為它們無法被 SwiftData 的 Schema 檢測。新的 Codable 支援解決了此問題。

*   **關鍵 API**：`@Attribute(.codable)`。
*   **功能**：標記該屬性為 Codable，SwiftData 會自動將該型別序列化（Serialization）為 Data 格式儲存，繞過 Schema 生成限制。

### 3. Results Observer (非 SwiftUI 環境)
對於非 SwiftUI 的應用架構（如遊戲引擎 SceneKit 或複雜的狀態管理類別），過去難以利用 `@Query` 的自動刷新特性。

*   **關鍵 API**：`ResultsObserver`。
*   **功能**：獨立於 SwiftUI View 生命週期之外，監控資料庫變更。配合 `withContinuousObservation`，當資料異動時，開發者可以即時執行邏輯（例如重新計算地圖範圍）。

### 4. History Observer
為了更精確地追蹤資料變更，特別是涉及同步機制（如外部伺服器同步）的場景，新增了歷史觀察功能。

*   **關鍵 API**：`HistoryObserver`。
*   **功能**：透過監控 `eventCounter` 的增長，開發者可以得知何時發生了新的歷史交易（Transaction），進而呼叫 `fetchHistory` 處理特定變更。

---

## 程式碼模式示範

### 如何進行資料分組 (Sectioning)
```swift
// 在 View 中定義 Query，以 destination 為分組依據
@Query(sort: \Trip.startDate, sectionBy: \.destination) 
private var trips: [Trip]

// 在 View body 中使用
ForEach(trips.sections) { section in
    Section(header: Text(section.id)) { // ID 為 destination 字串
        ForEach(section) { trip in
            TripListItem(trip: trip)
        }
    }
}
```

### 使用 Codable 儲存外部型別
```swift
@Model
class Trip {
    var name: String
    @Attribute(.codable) var mkMapItemIdentifier: MKMapItemIdentifier? // 解決無法自動生成 Schema 的問題
}
```

### 在非 SwiftUI 環境使用 ResultsObserver
```swift
class MapCameraController {
    private var observer: ResultsObserver<Trip>
    private var token: ObservationTrackingToken?

    init(context: ModelContext) {
        self.observer = ResultsObserver(context: context)
        self.token = withContinuousObservation(on: observer) {
            // 當資料庫變更時，觸發此處邏輯
            self.recalculateBounds(observer.results)
        }
    }
}
```

---

## 開發者實用重點 (Key Takeaways)

1.  **選擇性使用 Codable**：`@Attribute(.codable)` 是處理無法修改之第三方型別（如 MapKit 物件）的「逃生門」。但若該型別是你自己定義的，**強烈建議使用原生 `@Model` 定義**，因為 Codable 屬性將無法被 Predicate 過濾，也無法參與排序或索引。
2.  **變更管理**：對於涉及伺服器同步的應用，請優先考慮 `HistoryObserver`，並結合 `transaction author` 篩選功能，避免不必要的同步迴圈（例如：忽略由伺服器觸發的本地端更新）。
3.  **UI 效能**：`ResultsObserver` 與 `Query` 同樣具備高效的變更追蹤能力。在需要動態計算 UI 狀態（如地圖邊界、數據統計圖表）時，它是取代傳統手動通知機制的首選。
4.  **相容性注意**：使用 Codable 屬性時，請確保你的序列化邏輯具備良好的向後相容性，因為該屬性的結構變更不會自動觸發 SwiftData 的自動遷移 (Migration)。

<!-- resources -->

---

## 資源連結

- [SwiftData](https://developer.apple.com/documentation/SwiftData)
- [Adopting SwiftData for a Core Data app](https://developer.apple.com/documentation/CoreData/adopting-swiftdata-for-a-core-data-app)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/274/4/87fb1efb-9956-414e-8c99-f2579fe86da2/downloads/wwdc2026-274_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/274/4/87fb1efb-9956-414e-8c99-f2579fe86da2/downloads/wwdc2026-274_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/274/
