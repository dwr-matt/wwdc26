# Code-along: Build powerful drag and drop in SwiftUI

這是一份關於 WWDC 2026 Session「**Code-along: Build powerful drag and drop in SwiftUI**」的技術摘要。

---

# 技術摘要：在 SwiftUI 中構建強大的拖放功能

## 概述
本課程深入介紹了 SwiftUI 在 iOS/iPadOS/VisionOS 27 及 macOS 26 中引入的進階拖放（Drag and Drop）API。除了基礎的 `draggable` 與 `dropDestination` 之外，新版本新增了對**項目重新排序（Reordering）**、**多項目拖曳（Multi-item drag）**以及**拖放行為配置（Drag/Drop Configuration）**的強力支援，讓開發者能以更直覺且靈活的方式處理應用程式內的內容互動。

---

## 主要概念與 API 介紹

Apple 此次針對拖放 API 進行了三大核心擴展：

### 1. 重新排序 API (Reordering API)
*   **`.reorderable`**：應用於單一視圖（如 `ForEach` 中的卡片），使其可被拖曳並與其他同類項目重排。
*   **`.reorderContainer`**：定義一個區域，允許在其中進行跨列或跨組的項目重排，自動處理 placeholder 的顯示與空間分配。

### 2. 多項目拖曳 API (Drag Container API)
*   **`.dragContainer`**：當使用者觸發拖曳時，系統會調用此 closure。開發者可在此決定當前 drag session 要包含哪些額外的資料項目（例如：一次拖曳整疊卡片而非單張）。

### 3. 拖放配置 API (Drag & Drop Configuration)
*   **`.dragConfiguration`**：定義資料在拖曳時的「移動意圖」（例如：`.move` 而非預設的 `.copy`）。
*   **`.dragPreviewsFormation` / `.dropPreviewsFormation`**：控制拖曳時預覽圖的呈現樣式（如：`pile`, `list`, `stack`），增強視覺回饋。
*   **`.dropConfiguration`**：給予開發者在 drop 瞬間的最終決定權，包含驗證規則、選擇目標（destination）以及處理轉移動作。

---

## 程式碼示範模式

### 實作重新排序 (Reordering)
在 `ForEach` 上添加 `reorderable`，並在父層 Stack 上設定 `reorderContainer`：

```swift
ForEach(cards, id: \.id) { card in
    CardView(card)
        .reorderable() // 標記為可重排
}
.reorderContainer(CardValue.self) { diff in
    // 處理 cards 陣列的更新邏輯
    cards.apply(diff)
}
```

### 處理多項目拖曳 (Multi-item)
透過 `dragContainer` 獲取當前項目，並回傳需要連帶移動的資料集合：

```swift
.dragContainer(CardValue.self) { itemIdentifier in
    // 回傳該項目及其上方的所有卡片資料
    return gameLogic.cardsAbove(itemIdentifier)
}
.dragPreviewsFormation(.stack) // 設定預覽為堆疊樣式
```

### 設定移動意圖 (Move vs. Copy)
使用 `dropConfiguration` 確保卡片是從牌組「移動」至牌堆，而非複製：

```swift
.dropConfiguration(for: CardValue.self) { session in
    let destination = determineTargetPile(session)
    return .move(to: destination) // 強制設定為移動
}
```

---

## 開發者實用重點

1.  **區分資料來源**：若有不想被重排的項目（如背面朝上的卡片），應將其放在獨立的 `ForEach` 視圖中，不要給予 `.reorderable()` 修飾符。
2.  **配置優先順序**：`dropConfiguration` 具有最終決定權。即便 `dropDestination` 接受了資料，若 `dropConfiguration` 返回了無效的操作，系統將拒絕該次放置（Drop）。
3.  **一致的視覺呈現**：建議在根視圖（Root Layout）設定 `dropPreviewsFormation`，以確保應用程式內所有目標區域的預覽效果一致。
4.  **體驗優化**：利用 `stack` 預覽模式來處理多項目拖曳，這能有效降低使用者在拖曳大量物件時的視覺混亂感。
5.  **平台相容性**：
    *   `dragContainer`：支援 iOS, iPadOS, VisionOS 27+。
    *   所有 API：完整支援 macOS 26+。

透過這些新 API，開發者無需手動編寫繁雜的 Gesture 與座標計算邏輯，即可實現如同原生系統般的流暢拖放體驗。

<!-- resources -->

---

## 資源連結

- [Making a card game with drag, drop, and reordering in SwiftUI](https://developer.apple.com/documentation/SwiftUI/Making-a-card-game-with-drag-drop-and-reordering-in-swiftui)
- [Drag and drop](https://developer.apple.com/documentation/UIKit/drag-and-drop)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/271/5/07f08d32-e28e-476f-8ebe-a3600b2e917c/downloads/wwdc2026-271_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/271/5/07f08d32-e28e-476f-8ebe-a3600b2e917c/downloads/wwdc2026-271_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/271/
