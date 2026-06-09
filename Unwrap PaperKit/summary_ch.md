# Unwrap PaperKit

這份技術摘要旨在協助開發者快速掌握 **WWDC 2026: Unwrap PaperKit** 的核心技術與應用場景。

---

# Session 摘要：Unwrap PaperKit

## 概述
PaperKit 是 Apple 平台上強大的畫布（Canvas）體驗引擎，廣泛應用於「備忘錄（Notes）」、「預覽程式（Preview）」以及 macOS 的「無邊記（Freeform）」。透過 PaperKit，開發者可以在自己的應用程式中整合繪圖、形狀標註、圖像處理及互動式文件編輯功能。本屆 WWDC 介紹了如何透過 PaperKit 的資料模型與 API，對畫布上的元素進行精細控制、自訂互動行為，以及利用「飾品（Adornments）」建立互動式 UI。

---

## 主要概念與 API

### 1. PaperMarkup 與資料模型
*   **PaperMarkup**：畫布的核心資料結構。
*   **subElements**：這是 PaperMarkup 中的核心屬性，是一個 `MarkupOrderedSet`（有序集合），開發者可藉此對畫布上的所有元素（形狀、圖片、文字等）進行讀取與修改。

### 2. Markup 協定與互動控制
每個畫布元素均遵循 `Markup` 協定，提供共同屬性（如：`frame`、`rotation`）。
*   **allowedInteractions**：這是本屆新增的關鍵 API，屬於 `MarkupInteractions` 選項集（OptionSet）。
*   **精細化管理**：開發者可針對單一元素限制其移動、調整大小、旋轉、刪除或樣式修改。
*   **ReadOnly 模式**：提供 `.readOnly` 旗標，可一次鎖定元素的所有互動，非常適合模板（Template）設計。

### 3. Adornments（裝飾層）
*   **定義**：Adornments 是一種位於畫布上方、錨定於畫布座標的視覺覆蓋層。
*   **特性**：
    *   **不進入資料流**：不會被儲存、列印或導出至原始文件內容中。
    *   **動態跟隨**：會自動追蹤畫布的縮放（Zoom）與捲動（Scroll）。
    *   **適用場景**：按鈕、互動式註解或協作介面。

---

## 程式碼模式與應用

### 修改元素互動行為 (設定 Read-Only)
若要防止使用者修改畫布上的模板形狀，可設定其互動屬性：
```swift
// 將模板元素設定為唯讀
shapeElement.allowedInteractions = .readOnly
```

### 迭代修改元素屬性
若需批次更新畫布元素（如修改形狀的填滿與邊框顏色）：
```swift
for element in paperMarkup.subElements {
    if var shape = element as? ShapeElement {
        shape.strokeColor = .blue
        shape.fillColor = .lightBlue
    }
}
// 最後更新 View Controller 的 markup
viewController.markup = paperMarkup
```

### 建立 Adornment 並處理觸發
```swift
// 1. 建立 Adornment
let buttonAdornment = MarkupAdornment(anchor: centerPoint, icon: myImage)
viewController.adornments = [buttonAdornment]

// 2. 透過 delegate 處理點擊
func didTapAdornmentWithID(id: UUID) {
    // 觸發自訂邏輯，例如開啟 Image Playground
    presentImagePlayground()
}
```

---

## 開發者實用重點

1.  **與 PencilKit 深度整合**：PaperKit 是基於 PencilKit 建構的。繪圖筆觸會自動成為 `MarkupElement`，並支援 PencilKit 最新的 API（如字元識別、Bezier 路徑轉換）。
2.  **無需複雜的狀態管理**：透過 `MarkupOrderedSet`，可以直接對畫布內容進行宣告式的修改，系統會自動處理渲染與對應變更。
3.  **UI 與資料分離**：利用 Adornments 處理應用程式所需的控制元件（如：在畫布上加個「產生圖片」的按鈕），確保應用程式邏輯與使用者編輯的文件內容互不干擾。
4.  **建議延伸閱讀**：
    *   *Reading between the strokes with PencilKit* (深入了解筆觸識別與轉換)
    *   *Create High-Quality Images Using Image Playground* (結合 AI 生成內容至畫布中)

---
*註：本文內容根據 WWDC 2026 Session 逐字稿整理，建議開發者於正式環境中使用時，參考 Apple 官方最新的 Developer Documentation。*

<!-- resources -->

---

## 資源連結

- [PaperKit](https://developer.apple.com/documentation/PaperKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/372/4/012a7de6-cf54-420f-aaf7-02ea568485bf/downloads/wwdc2026-372_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/372/4/012a7de6-cf54-420f-aaf7-02ea568485bf/downloads/wwdc2026-372_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/372/
