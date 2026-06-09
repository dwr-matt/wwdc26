# Make your game great with touch

這是一份針對 WWDC 2026 Session「Make your game great with touch」的技術摘要。

---

# 技術摘要：為遊戲打造出色的觸控體驗 (Make your game great with touch)

## 概述
本 Session 旨在協助開發者將已具備控制器或鍵鼠支援的 Mac/Console 遊戲，優化並移植至 iOS 與 iPadOS。重點不僅在於「移植」，而是如何利用 Apple 新推出的 **Touch Controller Framework**，針對觸控螢幕重新設計 UI 互動，創造流暢、直覺且不干擾遊戲畫面的體驗。

---

## 主要概念、API 與框架介紹

### 1. Touch Controller Framework
這是本次介紹的核心框架，它擴展了現有的 `GameController` (GC) 框架，讓觸控輸入能以 `GCController` 物件的形式呈現，使開發者能沿用原本處理實體手把的邏輯來處理觸控輸入。

*   **API 特色**：
    *   **高效能**：直接與 Metal API 整合，確保渲染觸控介面時能達到最高效能。
    *   **一致性**：當觸控控制器啟用時，系統會將其視為一個 `GCController`，開發者可以透過 `valueChangedHandler` 監聽輸入變化。

### 2. 靈活的佈局系統 (Flexible Layouts)
為了適應 iPhone 與 iPad 各種不同螢幕比例，框架提供了：
*   **九宮格錨點 (Anchor Points)**：為每個控制項分配錨點（如左下、右下、頂部等），並定義相對於錨點的偏移量 (Offset)。
*   **適應性設計**：當裝置螢幕尺寸變化時，控制項會維持與錨點的距離，確保在所有裝置上都能維持舒適的操作距離。

### 3. 安全區域 (Safe Areas)
在設計全螢幕遊戲時，必須考量：
*   **系統介面避讓**：透過 UIKit 的 Safe Area Insets，避免控制項被瀏海、動態島 (Dynamic Island) 或 Home 指示條遮擋。

---

## 示範的程式碼模式與技術

### 1. 初始化與啟用觸控控制器
開發者需要透過 Descriptor 來建立並啟用控制器：
```swift
// 建立控制器物件
let controller = TCTouchController(descriptor: myDescriptor)
// 啟用控制器，讓遊戲開始接收輸入
controller.connect() 
```

### 2. UI 事件處理 (UIKit 整合)
在 `UIView` 中攔截觸控事件並轉交給框架：
```swift
override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
    touchController.handleTouchBegan(touches, with: event)
}
// 同樣需要實作 touchesMoved 與 touchesEnded
```

### 3. 動態控制項顯示 (Conditional UI)
不要將所有實體按鍵一對一映射到螢幕上，應根據情境動態調整：
*   **隱藏未用按鈕**：使用 `isEnabled = false` 或直接從控制器移除。
*   **動態圖示更新**：根據遊戲狀態（例如切換武器）更換控制項的 Glyph (圖示)，減少玩家查閱設定的時間。

### 4. 觸控手勢的特殊優化
*   **隱形搖桿 (Invisible Thumbsticks)**：設定 `hidesWhenNotPressed = true`，僅在觸控時顯示，增加螢幕可視空間。
*   **觸控板 (Touchpad)**：使用 `TCTouchpad` 代替右搖桿，解決旋轉過快或遲滯問題。設定 `reportsRelativeValues = true`，讓玩家在螢幕右側任意區域滑動即可操作鏡頭。
*   **手勢組合優化**：將原本需要多指同時按下的複雜動作（如衝刺、瞄準、施法），整合至單一互動區域，利用搖桿推動幅度或長按拖曳來觸發。

---

## 開發者的實用重點

1.  **勿直接移植 (Don't Copy-Paste)**：不要嘗試將實體手把所有的按鈕配置都塞在螢幕上。觸控介面應該是「動態」且「情境式」的。
2.  **善用全螢幕區域**：
    *   **角色移動**：將左側螢幕區域作為搖桿的觸發範圍，增加操作精準度。
    *   **鏡頭控制**：將右側螢幕區域作為觸控板。
3.  **提供明確視覺回饋**：
    *   預設的觸控控制項雖有基本按壓效果，但建議針對關鍵動作（如衝刺時）增加自訂的視覺效果（如外圈發光 Halo），讓玩家清楚目前的狀態。
4.  **自動化移除 (Auto-dismiss)**：針對非永久性 UI（如力量選單），若玩家在一段時間（如 3 秒）內未進行操作，應自動隱藏以保持畫面整潔。
5.  **核心原則**：設計時應以「雙手持握操作」為基礎，並考量玩家手指觸碰區域，確保重要按鈕位於拇指舒適的操作半徑內，同時避免遮擋遊戲角色或核心動作區。

---
*這份摘要提供了一個快速入門的框架，建議開發者進一步參考 Apple 的官方 Sample Code 並結合 `GameController` 框架文件進行實作。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/358/4/fdd21d54-a233-49d4-8d00-4dc51284515d/downloads/wwdc2026-358_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/358/4/fdd21d54-a233-49d4-8d00-4dc51284515d/downloads/wwdc2026-358_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/358/
