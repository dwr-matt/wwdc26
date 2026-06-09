# Modernize your AppKit app

這是一份關於 WWDC 2026 Session「Modernize your AppKit app」的詳細技術摘要。

---

# 技術摘要：現代化你的 AppKit 應用程式 (Modernize your AppKit app)

## 概述
本場 Session 旨在引導開發者如何透過現代化的 API 與設計模式，讓傳統的 AppKit 應用程式與 macOS 系統環境達到完美的「和諧感」（Harmony）。講師 Udjoaini 強調現代化應用程式的三大關鍵領域：**精確的輸入控制、流暢的應用程式生命週期管理（連續性），以及符合 macOS 27 設計語言的視覺體驗**。

---

## 主要概念與技術亮點

### 1. 精確輸入方法 (Precision Input)
AppKit 正逐步從傳統的「Mouse Down + Tracking Loop」模式轉向與 SwiftUI、UIKit 同步的**手勢辨識器 (Gesture Recognizers)** 系統，以提供更具彈性且跨框架一致的互動體驗。

*   **View-based APIs：** 針對選取（Selection）、右鍵選單（Context Menus）、拖放（Drag and Drop）與文字選取，應改用專屬的 API，而非手動重寫 `mouseDown`。
*   **Control Events：** 現在 AppKit 的控制項（如 `NSButton`, `NSSlider`）支援與 UIKit 類似的 Target-Action 控制事件，無需子類化控制項即可處理複雜互動。
*   **文字選取：** 引入 `NSTextSelectionManager`，讓非 `NSTextView` 的自定義視圖也能獲得標準的 macOS 文字選取行為。

### 2. 應用程式生命週期與連續性
為了確保應用程式能「隨時關閉並快速恢復」，重點在於優雅地處理退出請求與狀態恢復。

*   **優雅退出：** 透過調整 `NSWindow` 的 `preventsApplicationTerminationWhenModal` 屬性，避免在非必要情況下阻止系統關閉應用程式。
*   **狀態恢復 (State Restoration)：** 使用 `NSWindowRestoration` 協議，透過 `encodeRestorableState` 與 `restoreState` 方法，將 UI 的層級狀態（而非整個資料庫）序列化保存，確保用戶重啟應用程式時能回到上次的視窗與操作位置。

### 3. macOS 27 視覺更新：Liquid Glass 與同心圓 (Concentricity)
*   **Liquid Glass 進化：** macOS 27 自動優化了滾動邊緣效果，且新增了 Glass 容器的「彈跳回饋 (Bounce Effect)」，讓控制項點擊時更有觸感。
*   **同心圓配置 (Concentricity)：** 透過新的 `Corner Configuration API`，讓視圖的圓角能隨著容器的圓角自動調整，維持視覺的一致性。

---

## 程式碼模式與技術實踐

### A. 使用 Control Events 取代子類化
```swift
// 現代化的做法，無需重寫 NSButton 子類
let button = NSButton(title: "Click Me", target: nil, action: nil)
button.addAction(UIAction { _ in
    print("Action triggered!")
}, for: .primaryActionTriggered)
```

### B. 狀態恢復 (State Restoration)
在視窗控制器中實作狀態恢復：
```swift
// 儲存狀態
override func encodeRestorableState(with coder: NSCoder) {
    coder.encode(selectedProductID, forKey: "productID")
    super.encodeRestorableState(with: coder)
}

// 恢復狀態
override func restoreState(with coder: NSCoder) {
    super.restoreState(with: coder)
    if let id = coder.decodeObject(forKey: "productID") as? String {
        self.applySelection(id)
    }
}
```

### C. 實作同心圓 (Concentricity)
```swift
override var cornerConfiguration: NSViewCornerConfiguration {
    return NSViewCornerConfiguration(
        radius: .containerConcentric,
        minimumRadius: 8.0
    )
}
```

---

## 開發者實用重點 (Key Takeaways)

1.  **審查 `mouseDown` 的使用：** 檢查專案中是否有過度依賴 `mouseDown` 的邏輯。若能改用 `NSTableViewDelegate`、`NSCollectionViewItem` 或手勢辨識器，請優先更換。
2.  **鍵盤導航優化：** 確保應用程式對於 Tab 鍵的移動路徑（Key View Loop）有良好的支援，特別是自定義的 `NSStatusItem`，應正確實作 `Expanded Interface Session API`。
3.  **避免 `hitTest` 衝突：** 若遇到控制項無法點擊，優先檢查是否有重疊的透明視圖，並適度使用 `hitTest(_:with:)` 返回 `nil` 來傳遞事件。
4.  **自動化恢復：** 為所有視窗設定 `autosaveName`，並確保 `isRestorable` 為 `true`，這是讓應用程式給用戶「專業感」最簡單的手段。
5.  **視覺協調：** 在邊角處的元件，務必採用 `CornerConfiguration`，這能讓你的 App 看起來像是原生 macOS 的一部分，而非僅是「在 Mac 上運行的視窗」。

---
*編輯備註：本場次鼓勵開發者擁抱 SwiftUI 與 AppKit 的共生關係，特別是在處理選單列與狀態欄時，建議參考「Use SwiftUI with AppKit and UIKit」以獲得最佳實踐。*

<!-- resources -->

---

## 資源連結

- [Use SwiftUI with AppKit](https://developer.apple.com/videos/play/wwdc2022/10075/)
- [Restoring your app’s state with AppKit](https://developer.apple.com/documentation/AppKit/restoring-your-app-s-state-with-appkit)
- [Gestures](https://developer.apple.com/documentation/AppKit/gestures)
- [TN3212: Adopting gesture recognizers for Sidecar touch support](https://developer.apple.com/documentation/Technotes/tn3212-adopting-gesture-recognizers-for-sidecar-touch-support)
- [NSControl.Events](https://developer.apple.com/documentation/AppKit/NSControl/Events)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/289/5/6a2a7cfa-56a1-4cbb-ae54-1f229e1708ae/downloads/wwdc2026-289_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/289/5/6a2a7cfa-56a1-4cbb-ae54-1f229e1708ae/downloads/wwdc2026-289_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/289/
