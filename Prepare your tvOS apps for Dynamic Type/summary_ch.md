# Prepare your tvOS apps for Dynamic Type

這份摘要為您整理了 WWDC 2026 Session「Prepare your tvOS apps for Dynamic Type」的核心技術重點，協助您快速掌握如何讓 tvOS 應用程式支援動態字級 (Dynamic Type)。

---

# 技術摘要：為 tvOS 應用程式導入 Dynamic Type

## 概述
隨著 tvOS 27 的推出，Apple 將「動態字級 (Dynamic Type)」功能導入 tvOS 平台，提供系統級的文字縮放支援。這項功能允許使用者根據需求調整電視上的文字大小，不僅提升了無障礙體驗，也讓 app 能適應更多使用情境。本課程重點說明如何運用 UIKit 與 SwiftUI 實現自動縮放，並指導開發者如何優化排版以適應變大的文字，同時建議開發者在 App Store 的無障礙標籤（Accessibility Nutrition Labels）中標註此項支援。

## 主要概念與框架

### 1. 核心機制
*   **動態字級運作方式**：tvOS 的 Dynamic Type 與 iOS 運作邏輯一致。系統會根據使用者在「設定」>「輔助使用」>「顯示」>「文字大小」中的設定，自動調整應用程式的文字比例。
*   **支援的縮放範圍**：從「大 (Large)」一直到「輔助使用超超超大 (Accessibility Extra Extra Extra Large)」。

### 2. 開發框架
*   **UIKit 與 SwiftUI**：預設的標準元件（如 `UILabel`, `UIButton`, `Text`, `NavigationStack` 等）已內建對 Dynamic Type 的自動支援。
*   **輔助使用標籤 (Accessibility Nutrition Labels)**：開發者應在 App Store Connect 中更新標籤，主動通知用戶您的 app 支援此功能，以吸引需要較大字體的用戶。

## 示範的程式碼模式與技術

### 移除硬編碼 (Hard-coding)
避免在 UI 中使用硬編碼的字體大小或尺寸約束 (Constraints)。

*   **SwiftUI 修改建議**：
    *   **字體**：將固定字體大小替換為語意化風格（例如：`.caption`）。
    *   **寬度約束**：移除硬編碼的寬度（如 `.frame(width: 300)`），改用彈性佈局。
    ```swift
    // 修正前：固定寬度導致截斷
    // Text("Sign-up Info").font(.system(size: 16)).frame(width: 300)

    // 修正後：使用彈性佈局
    Text("Sign-up Info")
        .font(.caption) // 使用語意化樣式
        .frame(maxWidth: .infinity) // 允許空間隨內容自動擴展
    ```

*   **UIKit 修改建議**：
    *   必須確保設定 `adjustsFontForContentSizeCategory = true`。
    *   更新字體樣式至標準 Text Styles（如 `UIFontTextStyleBody`）。

### 條件式佈局 (Conditional Layout)
當文字變大時，原本的橫向排列可能空間不足，這時需要動態調整 UI。

*   **偵測字體大小**：使用 `@Environment(\.dynamicTypeSize)` 讀取目前的動態字級狀態。
*   **AnyLayout 技巧**：在 SwiftUI 中，利用 `AnyLayout` 動態切換 `HStack` 與 `VStack`。
    ```swift
    let layout = dynamicTypeSize >= .accessibility1 ? AnyLayout(VStackLayout()) : AnyLayout(HStackLayout())

    layout {
        Image(systemName: "star")
        Text("Movie Title")
    }
    ```
*   **UIKit 處理方式**：使用 `UIStackView` 並根據 `preferredContentSizeCategory` 修改 `axis` 屬性。需呼叫 `registerForTraitChanges` 來監聽字體大小變更。

## 開發者實用重點 (Action Plan)

1.  **盤點現有程式碼**：全域搜尋專案中是否有硬編碼的 `UIFont` 大小，以及是否有固定數值的 `width` 或 `height` 約束。
2.  **轉換為語意化樣式**：全面採用 SwiftUI 或 UIKit 的標準系統字體風格，讓系統自動處理縮放邏輯。
3.  **測試不同層級**：務必在模擬器或實機上，將文字設定切換至最大（Extra Extra Extra Large），檢查是否發生文字截斷 (Truncation) 或 UI 元素重疊 (Clipping)。
4.  **優化佈局響應**：當文字變大導致元件擁擠時，應主動將排版從「橫向」調整為「縱向」，或減少顯示的項目數量（例如從 6 欄改為 4 欄）。
5.  **更新 App Store 資訊**：完成無障礙測試後，記得在 App Store 的無障礙設定中標註支援更大字體，提升產品的專業度與包容性。

<!-- resources -->

---

## 資源連結

- [Applying custom fonts to text](https://developer.apple.com/documentation/SwiftUI/Applying-Custom-Fonts-to-Text)
- [Scaling fonts automatically](https://developer.apple.com/documentation/UIKit/scaling-fonts-automatically)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/221/5/ada10ebd-34f8-4f57-92b5-4b3cd6281267/downloads/wwdc2026-221_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/221/5/ada10ebd-34f8-4f57-92b5-4b3cd6281267/downloads/wwdc2026-221_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/221/
