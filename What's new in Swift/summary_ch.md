# What's new in Swift

這是一份關於 WWDC 2026 Session「What's new in Swift」的詳細技術摘要。本次更新涵蓋了 Swift 6.3 與 6.4 的多項重要改進，重點在於**提升程式碼編寫效率、增強硬體效能調校、以及擴展 Swift 在跨平台（Web、嵌入式）的互操作性**。

---

### 1. 概述
本場演講由 Swift 團隊的 Becca 與 Evan 主講，詳細介紹了 Swift 6.3 與 6.4 的新功能。主要目標是「讓開發者能精確表達意圖，同時不犧牲安全性」。內容涵蓋：
*   **語言層面的優化**：減少冗餘語法，增強模組管理。
*   **API 與標準函式庫更新**：針對 Concurrency、測試與檔案操作提供更現代化的處理方式。
*   **效能調校**：引入更強大的 Ownership 模型，減少不必要的記憶體拷貝。
*   **跨平台擴展**：Swift 與 C/C++/Java 的深度整合，以及對 WebAssembly 與嵌入式系統的加強支援。

---

### 2. 主要概念與 API 更新

#### A. 語言與工具改進
*   **語法簡化**：移除 `some` 與 `any` 在 Optional 型別中的括號限制。
*   **錯誤處理**：新增對未處理的 Concurrency Task 錯誤的編譯器警告。
*   **Availability 管理**：引入 `any Apple OS` 語法，簡化跨平台版本的宣告邏輯。
*   **Module Selector (`::`)**：解決不同模組間出現命名衝突的問題。使用 `Module::Type` 明確指定來源，即使兩模組同時匯入也不會發生歧義。
*   **`@diagnose` 屬性**：允許開發者在特定宣告範圍內精細控制警告或錯誤行為（例如忽略廢棄警告、強制將特定警告轉為錯誤）。

#### B. 標準函式庫與框架
*   **Task Cancellation Shield**：確保在任務取消後，關鍵的收尾工作（如檔案寫入）仍能安全完成。
*   **`mapKeyedValues`**：字典轉換時可同時獲取 Key 與 Value，提升處理效率。
*   **`FilePath`**：標準庫新增跨平台檔案路徑型別。
*   **Swift Testing**：支援非致命錯誤紀錄（Warning 層級）、動態跳過測試、測試重複執行以及與 XCTest 的雙向兼容性。
*   **Subprocess 1.0**：現代化子行程管理，支援 `AsyncBufferSequence` 進行流式輸出處理。
*   **Foundation Modernization**：持續移除 Objective-C 舊程式碼，改用 Swift 重寫以提升效能（如 `Data` 與 `URL` 處理）。

#### C. Ownership 與效能調校
*   **`inline always` 與 `inline never`**：賦予開發者對函數內嵌行為的直接控制權。
*   **`@specialized`**：手動指定泛型函數的具體化型別，優化關鍵路徑效能。
*   **Ownership 進階功能**：
    *   `Equatable`/`Comparable` 支援非拷貝（non-copyable）型別。
    *   **Iterable 協議**：支援 `for` 迴圈直接借用（borrow）數據，避免拷貝開銷。
    *   **`borrow` 與 `mutate` 存取器**：取代傳統 `get`/`set`，在屬性存取時避免拷貝大物件，適用於 `UniqueBox` 等自訂儲存容器。
    *   **`Ref` 與 `MutableRef`**：提供對單一值的安全借用與可變存取，解決多次查詢（如字典 Lookup）帶來的效能浪費。

---

### 3. 示範程式碼模式

#### 解決模組衝突 (`::`)
```swift
// 舊方式：編譯器可能困惑
import Rocket
import ToyStore

let ship = SaturnV() // 錯誤：模糊不清

// 新方式：模組選擇器
let ship = Rocket::SaturnV() 
```

#### 使用 `borrow` 與 `mutate` 優化存取
```swift
struct UniqueBox<T> {
    private var data: T
    
    // 透過 borrow/mutate 避免不必要的拷貝
    borrowing var value: T {
        borrow { data }
    }
    
    mutating var value: T {
        mutate { &data }
    }
}
```

#### 跨語言互操作性 (`@C`)
```swift
@C
func computeLaunchWindow(duration: Int) -> Int {
    // Swift 實作並暴露給 C 使用
    return duration * 2
}
```

---

### 4. 開發者實用重點（Takeaways）

1.  **安全性與效能的權衡**：善用 `borrow` 和 `mutate` 存取器，這是 Swift 6.4 解決效能瓶頸（特別是處理大型結構體時）的利器。
2.  **Swift Testing 是首選**：若專案仍在使用 XCTest，現在可以逐步遷移，且兩者互操作性極佳，無需擔心覆蓋率流失。
3.  **跨平台發展**：Swift 不再僅限於 Apple 平台，通過 `Swift SDK for Android` 與 WebAssembly (WASM) 的支援，Swift 已成為前後端與嵌入式開發的強力候選語言。
4.  **精細的編譯器控制**：利用 `@diagnose` 屬性，針對安全性要求較高的區塊（如 security-critical functions）啟用嚴格檢查，不必全域修改專案設定。
5.  **參與社群**：Swift 團隊強調 Open Source 的重要性，建議開發者追蹤 `forums.swift.org`，並關注 Build/Packaging 工作組的最新進展。

這兩年的 Swift 更新展現了語言向「底層可控」與「跨平台通用」兩端同時邁進的企圖心，對於追求效能與程式碼維護性的團隊來說，現在正是導入這些新特性的好時機。

<!-- resources -->

---

## 資源連結

- [Swift Blog](https://www.swift.org/blog/)
- [Explore documentation on swift.org](https://www.swift.org/documentation/)
- [Swift Forums](https://forums.swift.org)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/262/5/d430e425-34fc-4ed5-b590-507ac593453a/downloads/wwdc2026-262_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/262/5/d430e425-34fc-4ed5-b590-507ac593453a/downloads/wwdc2026-262_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/262/
