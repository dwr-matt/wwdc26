# Use SwiftUI with AppKit and UIKit

這是一份為開發者整理的 WWDC 2026 Session **「Use SwiftUI with AppKit and UIKit」** 技術摘要。

---

# WWDC 2026 技術摘要：在 AppKit 與 UIKit 中整合 SwiftUI

## 概述
本 Session 由 UI Frameworks 團隊的 David Nadober 主講，重點在於如何將 SwiftUI 逐步導入既有的 AppKit 或 UIKit 應用程式。Apple 強調，SwiftUI 從設計之初就考慮了與舊有框架的共存，開發者無需全盤重寫，即可透過增量（Incremental）方式導入 SwiftUI 的現代化特性，提升開發效率並共享跨平台的實作邏輯。

---

## 主要概念與框架介紹

### 1. Observable 宏的自動更新機制
現在 AppKit 與 UIKit 支援透過 `Observable` 宏來自動同步資料模型（Model）與視圖。
*   **機制**：當資料模型中的屬性發生變化時，與其關聯的 `NSView` 或 `UIViewController` 會自動觸發重繪（Redraw）或更新，無需再手動呼叫 `needsDisplay = true`。
*   **支援的生命週期方法**：包括 `drawRect` (繪圖相關方法)、`updateConstraints`、`layout` 等。
*   **向下相容**：開發者可透過在 `Info.plist` 加入 `NSObservationTrackingEnabled`（macOS 15+）或 `UIObservationTrackingEnabled`（iOS 18+）來啟用此功能（2026 年之後版本預設開啟）。

### 2. SwiftUI 的現代化視圖整合
*   **Canvas**：SwiftUI 提供的 `Canvas` 提供即時模式（Immediate Mode）繪圖 API，類似於 AppKit 的 `drawRect`，適合處理自訂繪圖需求，並可透過 `WithCGContext` 複用既有的 Core Graphics 程式碼。
*   **NSHostingView**：作為銜接橋樑，將 SwiftUI 視圖嵌入既有的 AppKit 視圖階層中。
*   **NSGestureRecognizerRepresentable**：允許開發者將自訂的 `NSGestureRecognizer` 邏輯封裝並賦予 SwiftUI 視圖，例如處理特殊的「強制點擊」（Force Click）手勢。

### 3. 進階整合 API
*   **NSHostingMenu**：允許開發者使用 SwiftUI 構建菜單（Menu）並將其嵌入現有的 AppKit 主選單中，同時支援鍵盤快捷鍵。
*   **NSHostingSceneRepresentation**：允許在現有的 `NSApplicationDelegate` 中動態添加與移除 SwiftUI 場景（Scene），例如在狀態欄添加額外的選單圖示（Menu Bar Extra）。

---

## 示範的程式碼模式

### 透過 Observable 進行自動化更新
在 `NSView` 中，不再需要手動管理顯示狀態：
```swift
// 舊模式：手動觸發
func colorDidChange() {
    self.needsDisplay = true 
}

// 新模式：使用 Observable 宏，系統自動追蹤
override func draw(_ dirtyRect: NSRect) {
    // 當 model 改變時，系統會自動觸發此處繪製
    let color = myColorModel.currentColor 
    // ...繪製邏輯
}
```

### 將手勢識別器引入 SwiftUI
透過協議實作：
```swift
struct MyGestureRepresentable: NSGestureRecognizerRepresentable {
    func makeNSGestureRecognizer(context: Context) -> MyForceClickRecognizer {
        return MyForceClickRecognizer()
    }
    
    func handleNSGestureRecognizerAction(_ recognizer: MyForceClickRecognizer, context: Context) {
        // 處理手勢邏輯
    }
}
```

---

## 開發者實用重點

1.  **採用「增量式」策略**：不要為了使用 SwiftUI 而全面重構。建議從新功能的開發或舊有組件的維護開始，以 `NSHostingView` 進行局部導入。
2.  **善用 Observable 降低耦合**：將 Model 轉為 `Observable` 是過渡到 SwiftUI 最平滑的一步，這能讓既有的 AppKit/UIKit 程式碼立刻獲得更現代化的響應式更新能力。
3.  **繪圖邏輯的現代化**：對於需要複雜自訂繪圖的 UI，優先考慮 SwiftUI 的 `Canvas`，它在效能與開發體驗上提供了良好的平衡。
4.  **場景整合**：利用 `NSHostingSceneRepresentation` 可以在不更動應用程式核心架構的前提下，為 App 添加現代化的輔助視窗或選單列功能。
5.  **跨平台兼容性**：雖然本 Session 以 macOS 為主，但相同的概念與 API（如 `UIHostingView` 等）同樣適用於 iOS 及其他 Apple 平台，遵循「寫一次邏輯，多處使用」的原則。

**建議觀看順序**：
若對細節有興趣，官方推薦配合以下 Session 一併觀看：
*   *What's New in UIKit (WWDC 2025)*
*   *Use SwiftUI with AppKit / UIKit (WWDC 2022)*
*   *Compose Advanced Graphics Effects with SwiftUI (WWDC 2026)*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/272/5/e1e4aa9a-cbe2-4f83-9cea-3dcaae19afd6/downloads/wwdc2026-272_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/272/5/e1e4aa9a-cbe2-4f83-9cea-3dcaae19afd6/downloads/wwdc2026-272_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/272/
