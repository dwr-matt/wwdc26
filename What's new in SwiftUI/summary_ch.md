# What's new in SwiftUI

這是一份關於 WWDC 2026「What's new in SwiftUI」Session 的詳細技術摘要，旨在幫助開發者快速掌握 SwiftUI 在 2027 版本中的重大更新。

---

# WWDC 2026 技術摘要：What's new in SwiftUI

## 概述
本 Session 由 Apple UI Frameworks 工程師 Steven 與 Julia 主講，重點介紹了 SwiftUI 在 2027 版本中的重大升級。內容涵蓋視覺設計風格（Liquid Glass）、Document API 的強化、UI 互動與呈現的改進，以及效能優化（包括資料流、型別檢查與快取機制）。透過這些更新，開發者能更輕鬆地打造高效能、高適應性的應用程式。

---

## 主要概念與 API 更新

### 1. 視覺與互動體驗
*   **Liquid Glass 設計語言**：SwiftUI 應用程式將自動獲得新的設計外觀，支援動態 Tint 調整。
*   **互動性增強**：macOS 與 iPadOS 應用程式現在可以更細膩地處理指標（Pointer）互動與視窗狀態（例如：非活動狀態時自動調暗圖示與文字）。
*   **Resizability（可調整大小）**：iPhone 應用程式在 iOS 27 中支援動態調整大小，Xcode 27 Live Previews 新增了調整大小的手把供測試。
*   **Toolbar 優化**：
    *   `visibility priority`：設定工具列項目的優先級（High Priority）。
    *   `toolbar overflow menu`：指定哪些功能應自動移入溢出選單。
    *   `top bar pin trailing`：固定工具列項目位置。
    *   `toolbar minimize behavior`：支援捲動時自動隱藏導覽列（on scroll down）。

### 2. 強化版 Document API
*   **Document Creation Context**：新增 `DocumentCreationSource`，允許開發者針對不同啟動方式（例如「空白」或「從照片啟動」）提供自訂邏輯。
*   **效能優化 (Writable/Readable Document)**：
    *   採用 `Observable` 巨集以減少不必要的視圖刷新。
    *   `WritableDocument` 與 `DocumentWriter` 允許非同步且非隔離（Non-isolated）的寫入操作，避免 UI 卡頓。
    *   新增 `progress` 參數，利用 `FoundationSubProgress` 顯示檔案儲存進度。
*   **跨格式匯出**：透過擴展 `WritableContentTypes` 並實作 `CoreGraphics` 轉換，可將 Document 匯出為 PNG 等格式。

### 3. 呈現與互動 (Presentation & Interaction)
*   **Reorderable API**：新增 `Reorderable` 修飾符與 `ReorderContainer`，適用於 `List` 與 `LazyVGrid`，自動處理拖曳排序邏輯。
*   **Swipe Actions**：不再侷限於 `List`，現在支援任何視圖使用 `swipe actions` 修飾符。
*   **Confirmation Dialogs**：支援與 `Sheet` 相同的「項目綁定」模式（Item Binding）。

### 4. 資料流與效能優化
*   **AsyncImage 快取**：現在原生支援 HTTP 快取規範，可透過 `URLSession` 修飾符進一步自訂快取策略。
*   **State 巨集優化**：`@State` 現在變為「懶加載（Lazy）」，宣告在其中的 class 物件僅會在首次初始化，不會在後續視圖更新時重複產生。
*   **編譯效能提升**：引入 `ContentBuilder`，簡化了複雜巢狀視圖（Section, Group, ForEach）的型別判斷，顯著縮短編譯時間。

---

## 程式碼示範技術

### 設定工具列優先級與溢出選單
```swift
// 設定高優先級
.toolbar {
    ToolbarItem(placement: .primaryAction) {
        Button("Undo") { ... }.visibilityPriority(.high)
    }
    // 放入溢出選單
    ToolbarOverflowMenu {
        Button("Export") { ... }
        Button("Clear") { ... }
    }
}
```

### 使用 Reorderable API
```swift
List {
    ForEach(stickers) { sticker in
        Text(sticker.name)
    }
    .onReorder(perform: moveStickers) // 呼叫排序邏輯
}
.reorderableContainer() // 啟用排序容器
```

---

## 開發者實用重點（Key Takeaways）

1.  **善用 Xcode 27 的「Agent Skills」**：Xcode 內建了「SwiftUI Specialist」與「What's New in SwiftUI」輔助工具，能指導開發者將既有程式碼轉移至新的 API。
2.  **移除多餘的初始化邏輯**：由於 `@State` 現在會自動優化初始化過程，請檢查程式碼中是否有在 `init` 方法中對 `@State` 變數重複賦值的問題，以免觸發編譯錯誤。
3.  **UI 彈性化**：針對 iPhone 應用程式的 resizability，若專案中混用了 UIKit，請務必參考「Modernize Your UIKit app」相關指引，改用 `Size Classes` 進行佈局判斷而非硬編碼（Idiom）。
4.  **效能監控**：善用 `DocumentWriter` 的進度報告功能與 `AsyncImage` 的快取策略，這能直接顯著提升使用者的流暢感受。

建議開發者優先將專案升級至 **Xcode 27**，並嘗試針對複雜視圖結構引入 `ContentBuilder` 概念，以改善開發過程中的編譯速度。

<!-- resources -->

---

## 資源連結

- [State()](https://developer.apple.com/documentation/SwiftUI/State())
- [ContentBuilder](https://developer.apple.com/documentation/SwiftUI/ContentBuilder)
- [Swift Collections on GitHub](https://github.com/apple/swift-collections)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/269/4/9215cf93-1308-4706-91e8-34d4e40939d1/downloads/wwdc2026-269_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/269/4/9215cf93-1308-4706-91e8-34d4e40939d1/downloads/wwdc2026-269_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/269/
