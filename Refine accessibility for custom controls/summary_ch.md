# Refine accessibility for custom controls

這份技術摘要旨在協助開發者掌握 **"Refine accessibility for custom controls" (優化自訂控制項的輔助使用功能)** 的核心重點。

---

# WWDC 2026 技術摘要：優化自訂控制項的輔助使用 (Accessibility)

## 概述
本課程探討如何為 App 中的自訂 UI 控制項構建完善的輔助使用體驗。開發者通常會設計超越標準 UI 元件的創意交互，但這些設計若缺乏輔助技術（如 VoiceOver、Switch Control）的支援，將導致視障或低視能使用者無法操作。Kin 通過三個實際範例（咖啡機滑桿、等化器面板、虛擬貓咪互動），展示如何透過 SwiftUI 修飾符將視覺線索轉化為無障礙互動。

---

## 主要概念與 API 介紹

為了確保自訂控制項具備良好的輔助使用性，必須將視覺資訊轉化為以下四個維度：
1. **目的 (Purpose)**：控制項是用來做什麼的？
2. **數值 (Value)**：當前的狀態或數值為何？
3. **動作 (Actions)**：使用者可以執行哪些操作？
4. **反饋 (Feedback)**：操作後的狀態變化如何傳達？

### 關鍵 API：
*   **`.accessibilityLabel()`**: 定義控制項的名稱與用途。
*   **`.accessibilityValue()`**: 提供當前狀態數值。
*   **`.accessibilityAddTraits(.adjustable)`**: 賦予控制項「可調整」的特性（如滑桿）。
*   **`.accessibilityAdjustableAction()`**: 定義當使用者進行滑動調整（增加/減少）時的具體邏輯。
*   **`.accessibilityActivationPoint`**: 設定 VoiceOver 啟動手勢的觸發點。
*   **`.accessibilityAction()`**: 定義自訂動作，適用於非線性或複雜的交互。
*   **`.accessibilityDirectTouch()`**: 允許 VoiceOver 將觸控事件直接傳遞給 App，而不進行攔截（適用於繪圖、遊戲等特殊交互）。

---

## 程式碼模式與技術實作

### 1. 基礎滑桿類控制項 (Adjustable Trait)
對於連續數值的控制，使用 `.adjustable` 特性最為簡單高效：

```swift
// 咖啡機範例
Slider(value: $coffeeLevel, in: 0...20)
    .accessibilityLabel("咖啡分量")
    .accessibilityValue("\(Int(coffeeLevel)) 盎司")
    .accessibilityAddTraits(.adjustable)
    .accessibilityAdjustableAction { direction in
        switch direction {
        case .increment: coffeeLevel += 1
        case .decrement: coffeeLevel -= 1
        }
    }
```

### 2. 精準控制 (Pass-through Gesture)
當使用者雙擊並按住 (Double tap and hold) 時，觸發 Pass-through 模式，允許使用者直接操作該元件。
*   **技術要點**：務必設定 `accessibilityActivationPoint` 以符合當前數值位置，並限制反饋頻率（例如：間隔 0.3 秒更新一次），避免語音過於吵雜。

### 3. 多維度控制 (Custom Actions)
對於 2D 空間操作（如等化器），單一調整特性不足以描述。使用 `accessibilityAction` 定義多個方向的操作：

```swift
.accessibilityAction(named: "向右移動") { moveRight() }
.accessibilityAction(named: "向上移動") { moveUp() }
```

### 4. 直接觸控 (Direct Touch)
對於複雜的觸控手勢（如遊戲互動）：
*   **`requireActivation`**: 需雙擊才能啟用直接觸控，避免誤觸。
*   **`silentOnTouch`**: 當控制項自身已有音效反饋時，抑制 VoiceOver 的播報，避免兩者音訊重疊。

---

## 開發者實用重點 (Best Practices)

1. **優先檢查標準做法**：在開始之前，先問自己：「這個控制項是否與系統現有的 Slider 或 Picker 類似？」若是，儘量使用系統標準元件。
2. **平衡手勢複雜度**：Direct Touch 很強大，但非所有使用者都能執行複雜手勢。**務必同時提供簡單的自訂動作 (Custom Actions)** 作為替代方案。
3. **避免過度反饋**：在 Pass-through 模式下，更新頻率應適當（建議每 0.3 秒），並確保只有在數值真正改變時才進行廣播。
4. **驗證與測試**：
    *   開啟 **VoiceOver** 實際操作自己的 App。
    *   檢查所有互動是否都能在不依賴視覺的情況下被理解。
    *   測試是否所有動作都能對應到簡單的語音指令或手勢。

**總結：** 將視覺資訊轉化為結構化的輔助資料，不僅是為了法規合規，更是為了讓所有使用者都能感受到你 App 的交互靈魂。

<!-- resources -->

---

## 資源連結

- [Accessible controls](https://developer.apple.com/documentation/SwiftUI/Accessible-controls)
- [Accessible descriptions](https://developer.apple.com/documentation/SwiftUI/Accessible-descriptions)
- [Accessibility fundamentals](https://developer.apple.com/documentation/SwiftUI/Accessibility-fundamentals)
- [Creating accessible views](https://developer.apple.com/documentation/SwiftUI/creating-accessible-views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/220/4/945f8d34-8427-4476-ae75-34edc4a9c3f9/downloads/wwdc2026-220_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/220/4/945f8d34-8427-4476-ae75-34edc4a9c3f9/downloads/wwdc2026-220_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/220/
