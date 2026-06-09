# Migrate to Swift Testing

這是一份為開發者整理的 WWDC 2026 Session **「Migrate to Swift Testing」** 技術摘要。

---

# 技術摘要：遷移至 Swift Testing (Migrate to Swift Testing)

## 概述
本場 Session 介紹了如何將現有的 XCTest 專案平滑遷移至 Swift Testing。Swift Testing 是一個為 Swift 生態系統量身打造的現代化測試框架，具備語法簡潔、並行執行效率高等優勢。講者重點介紹了「測試框架互通性 (Test Framework Interoperability)」功能，這讓開發者能夠在不大幅重構的情況下，逐步將測試遷移至 Swift Testing，並利用參數化測試與 Exit Test 等進階功能提升程式碼覆蓋率與測試品質。

---

## 主要概念與 API 介紹

### 1. 核心構建區塊
*   **`@Test` Macro**: 用於標記函數作為測試案例，支援使用反引號 (backticks) 進行識別符命名（如 `` `test feature with name` ``）。
*   **`#expect(...)`**: 核心斷言巨集，取代了大部分 XCTest 的 `XCTAssert` 系列。
*   **`issue.record()`**: 用於標記非預期的失敗，作為 `XCTFail` 的現代替代方案。

### 2. 測試框架互通性 (Test Framework Interoperability)
這是遷移過程的關鍵功能，允許開發者在同一個專案中混用兩個框架，並安全地處理跨框架的斷言呼叫：
*   **跨框架問題報告**：當你在 Swift Testing 中呼叫 XCTest 的斷言（或反之），系統會透過互通性機制處理。
*   **運行模式 (Modes)**：在 Test Plan 設定中可調整：
    *   **Limited (預設)**：跨框架問題呈現為「警告」。
    *   **Complete**：跨框架問題提升為「錯誤」（新專案建議採用）。
    *   **Strict**：嚴格模式，發生跨框架問題時立即停止測試並拋出 Fatal Error。
    *   **None**：關閉互通性，不建議長期使用。

---

## 示範技術與模式

### 1. 逐步遷移策略
*   **小型單元遷移**：保留現有 XCTest，優先為新功能編寫 Swift Testing 測試。
*   **輔助函數重構**：將複雜的斷言邏輯移至 Helper Function，並透過調整參數（如 `sourceLocation`）確保錯誤能正確指向呼叫處。

### 2. 參數化測試 (Parameterized Testing)
透過在 `@Test` 中傳入引數，Swift Testing 會自動將每組輸入視為獨立測試案例，並**預設以並行方式執行**，大幅縮短執行時間：
```swift
@Test(arguments: [1, 2, 3])
func testExample(value: Int) {
    #expect(value > 0)
}
```

### 3. Exit Testing (隔離崩潰測試)
對於需要驗證 `precondition` 或程式崩潰邏輯的場景：
*   使用 `#expect` 配合預期的退出條件。
*   Swift Testing 會在子程序 (Child Process) 中執行該測試，確保崩潰不會中斷整個測試套件。

---

## 開發者實用重點

1.  **何時保留 XCTest**：
    *   UI 自動化測試 (UI Automation)。
    *   效能測試 (Performance Testing)。
    *   涉及 Objective-C 異常處理的程式碼（Swift 無法安全處理 Obj-C 例外）。

2.  **效能與除錯**：
    *   Swift Testing 原生支援並行 (Concurrency)，大幅提升測試執行速度。
    *   參數化測試能清楚顯示哪一組輸入導致失敗，無需像過去使用迴圈測試時還需手動除錯。

3.  **遷移實用指令**：
    *   若想在 Swift Package 中手動切換互通性模式，可使用環境變數：`SWIFT_TESTING_XCTEST_INTEROP_MODE` (設定為 `limited`, `complete`, `strict` 或 `none`)。

4.  **善用工具**：
    *   Xcode 的 Coding Assistant 支援遷移策略建議與部分自動化重構。
    *   務必將工具版本升級至 **Swift 6.4+** 以啟用完整的互通性支援。

5.  **開源參與**：
    *   Swift Testing 為開源專案，位於 GitHub `swiftlang` 組織下，歡迎開發者透過 Swift Evolution 參與提案與討論。

---
*註：這是一場前瞻性的技術 Session，建議開發者先從非核心的測試檔案開始實驗，並在 Test Plan 中切換至 `Complete` 模式以檢查是否有遺留的跨框架斷言。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/267/4/d54e4861-10d9-4d4d-9952-3fe311cd2dc4/downloads/wwdc2026-267_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/267/4/d54e4861-10d9-4d4d-9952-3fe311cd2dc4/downloads/wwdc2026-267_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/267/
