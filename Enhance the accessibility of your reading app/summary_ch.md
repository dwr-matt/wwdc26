# Enhance the accessibility of your reading app

這是一份關於 WWDC 2026 Session「Enhance the accessibility of your reading app」的技術摘要，旨在協助開發者優化閱讀類 App 的無障礙體驗。

---

# 技術摘要：提升閱讀類 App 的無障礙體驗

## 概述
本場 Session 探討了如何為長篇閱讀類 App 提供流暢且豐富的無障礙體驗。重點在於如何超越基本的 UI 元件無障礙設定，通過蘋果提供的原生框架與進階 API，實現語音合成（VoiceOver）、螢幕朗讀（SpeakScreen）與 Accessibility Reader 的深度整合，確保視障或低視能使用者能無縫地進行文本導航、選取與閱讀。

---

## 主要概念與框架介紹

### 1. 核心無障礙目標
講師 Josh 強調了三個優化重點：
*   **顆粒化文本導航（Granular Text Navigation）：** 讓輔助技術能流暢地在詞、句、行之間切換。
*   **連續閱讀體驗（Continuous Reading Experience）：** 在換頁時不產生中斷。
*   **全面文本選取（Comprehensive Text Selection）：** 讓使用者能透過輔助技術輕鬆選取與操作文字。

### 2. 推薦使用的原生框架
*   **UIKit / SwiftUI：** 原生 `UITextView`、`TextEditor` 或 SwiftUI 的 `Text` 元件（開啟選取功能）已內建大部分無障礙功能。應優先使用這些元件，因為它們預設實作了 `UITextInput` 協議。
*   **macOS (AppKit)：** 使用 `NSTextView` 即可獲得相同的無障礙優勢。

---

## 關鍵技術與 API 應用模式

### 1. 解決跨視圖導航：`accessibilityNextTextNavigationElement`
當文章被切分成多個 `UITextView` 時，VoiceOver 無法自動跳轉至下一頁。
*   **實作方法：** 為每個文本視圖實作 `accessibilityNextTextNavigationElement` 與 `accessibilityPreviousTextNavigationElement`，明確指定相鄰的文本容器。
*   **SwiftUI：** 使用 `.accessibilityLinkedGroup(id:in:)` 修飾符，將多個文字元素串聯。

### 2. 優化連續閱讀：`causesPageTurn`
解決分頁內容在朗讀時於頁尾停止的問題。
*   **技術應用：** 將 `.causesPageTurn` 特徵（Trait）應用於頁面的最後一個段落。當搭配 `AccessibilityScroll` 使用時，SpeakScreen 會在閱讀完當前頁面後自動翻頁。

### 3. 自定義 Action 與 Edit Rotor
透過自定義操作增加選取文字後的交互性（例如：存為推薦）：
*   **實作方法：** 在 `accessibilityCustomActions` 中加入自定義動作，並確保設定 `category` 為 `.edit`。這能讓使用者透過 VoiceOver 的「編輯轉子（Edit Rotor）」直接觸發功能。

### 4. 進階：處理非標準文本（Custom Text Input）
若 App 使用自定義渲染（如手寫筆記影像、掃描頁面），原生物件屬性將失效。
*   **解決方案：** 實作 `UITextInput` 協議。
*   **實作要點：**
    *   **Geometry 管理：** 實作 `selectionRects(for:)` 以回傳文本選取範圍的座標。
    *   **Tokenizer：** 提供自定義 `UITextInputStringTokenizer` 來處理行、詞、句的邊界計算。
    *   **互動整合：** 加入 `UITextInteraction` 以支援選取把手（Selection Handles）的視覺回饋。

---

## 開發者實用重點（Checklist）

1.  **優先採用原生：** 能用 `UITextView` 或 `TextEditor` 就不要自定義，它們已經處理好了最複雜的 `UITextInput` 實作。
2.  **落實稽核（Audit）：** 
    *   務必開啟 VoiceOver 測試「朗讀全部（Read-all）」手勢。
    *   測試「行（Lines）」轉子導航是否順暢。
    *   測試文字選取與自定義編輯動作的響應。
3.  **分頁處理：** 不要讓使用者手動滑動頁面，透過 `causesPageTurn` 特徵讓朗讀引擎自動接續。
4.  **相容性：** 若你的 App 支援 Accessibility Reader，良好的 `UITextInput` 實作將直接提升該工具在你的 App 內的解析品質。

---

**總結：** 提升無障礙體驗不是附加功能，而是透過 API 將閱讀的流暢感還給每一位使用者。對於複雜的客製化閱讀器，透過深入實作 `UITextInput`，你可以賦予掃描檔案或影像文字與原生文件相同的互動深度。

<!-- resources -->

---

## 資源連結

- [accessibilityNextTextNavigationElement](https://developer.apple.com/documentation/ObjectiveC/NSObject-swift.class/accessibilityNextTextNavigationElement)
- [editCategory](https://developer.apple.com/documentation/UIKit/UIAccessibilityCustomAction/editCategory)
- [accessibilityLinkedGroup(id:in:)](https://developer.apple.com/documentation/SwiftUI/View/accessibilityLinkedGroup(id:in:))
- [causesPageTurn](https://developer.apple.com/documentation/SwiftUI/AccessibilityTraits/causesPageTurn)
- [UITextInput](https://developer.apple.com/documentation/UIKit/UITextInput)
- [Accessibility for UIKit](https://developer.apple.com/documentation/UIKit/accessibility-for-uikit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/219/4/da70a3a7-e193-4513-904f-991788c1fa81/downloads/wwdc2026-219_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/219/4/da70a3a7-e193-4513-904f-991788c1fa81/downloads/wwdc2026-219_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/219/
