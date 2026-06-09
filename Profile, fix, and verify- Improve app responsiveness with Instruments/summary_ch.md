# Profile, fix, and verify: Improve app responsiveness with Instruments

這份技術摘要整理自 WWDC 2026 Session「Profile, fix, and verify: Improve app responsiveness with Instruments」。本場演講深入探討如何利用 Instruments 工具診斷並解決 iOS 應用程式中常見的效能問題與 UI 卡頓現象。

---

### 1. 概述：建立效能診斷流程
應用程式的流暢度取決於後台運作的複雜度（編譯器最佳化、記憶體配置、OS 排程等）。當發生 UI 卡頓時，核心診斷流程如下：
*   **檢查 CPU 使用率**：
    *   **高 CPU 使用率**：代表主執行緒（Main Thread）過載，需進行演算法優化或將任務移至背景。
    *   **低 CPU 使用率**：代表執行緒處於阻塞（Blocked）狀態，在等待 I/O、鎖（Lock）或 IPC。
*   **工具選擇**：
    *   **Time Profiler**：用於分析 CPU 繁忙時的程式碼瓶頸。
    *   **System Trace**：用於分析執行緒被阻塞時的系統狀態。
    *   **Swift Executors**：用於觀察 Swift 並發任務在執行器間的分配狀況。

---

### 2. 主要概念與新工具介紹

#### A. 新的分析模式：Top Functions
當 Call Tree 過於複雜或函數被多次呼叫時，火焰圖（Flame Graph）可能難以判讀。Instruments 新增了 **Top Functions** 模式：
*   **機制**：忽略調用層級，將所有相同函數的執行時間合併。
*   **指標**：使用「Self Weight」（在該函數內直接執行的指令時間）來計算，能迅速找出最耗時的單一函數。

#### B. 效能比較工具：Run Comparisons
這是 Instruments 的新功能，允許在同一個文件中比較兩個不同的效能追蹤數據（Baseline vs. Optimized）：
*   **delta 計算**：自動比對前後兩次的 Call Tree 節點，並標示效能增減（紅色為回歸/變慢，綠色為改進）。
*   **應用場景**：驗證重構後的實際成效，確保最佳化未引入額外的回歸問題。

#### C. Swift Executors Instrument
視覺化主執行緒（Main Actor）、全域並發執行器（Global Concurrent Executor）及自定義執行器的排程。能有效找出哪些任務佔用了不該佔用的主執行緒資源。

---

### 3. 示範的程式碼模式與優化技術

#### 消除「Existentials」的運行時開銷
*   **問題**：過度使用 Swift 的 `any`（Existentials）會導致編譯器在執行時進行型別擦除與拆箱（Unwrapping），產生大量的 `swift_projectBox_opaque_existential` 運算。
*   **解決方案**：將繪圖程式碼改寫為**具體型別（Concrete types）**或使用**泛型（Generics）**，使編譯器能進行更佳的靜態分發與最佳化。

#### 執行緒卸載（Thread Offloading）
*   **問題**：在 SwiftUI 視圖中直接執行耗時操作（如生成縮圖），會導致該任務繼承 Main Actor 上下文，搶佔 UI 更新資源。
*   **解法**：在 Task 初始化時加上 `@detached` 或確保其在全域執行器運行：
    ```swift
    // 範例：將任務移至全域執行器執行
    Task(priority: .userInitiated) {
        let result = await renderThumbnail()
        // 更新 UI 時再回到 Main Actor
        await MainActor.run { ... }
    }
    ```

#### 避免同步阻塞 I/O
*   **問題**：在大檔案寫入時使用同步 API（如 `Data.write`），導致主執行緒在 Disk I/O 完成前完全凍結。
*   **解法**：將檔案存取操作封裝在背景 Task 中，並使用 `OSSignpost` 標記區間，以便在 Instruments 中精準追蹤該操作的實際耗時與阻塞時間。

---

### 4. 開發者的實用重點（Key Takeaways）

1.  **一定要 Profile Release Build**：Debug Build 包含大量除錯符號與執行期檢查，會嚴重誤導效能數據。
2.  **善用 OS Signposts**：透過 `os_signpost` 自定義追蹤區間，能讓你在數萬行記錄中快速鎖定特定的功能邏輯（如 Lasso 工具的使用時段）。
3.  **解讀阻塞（Blocking）**：如果 UI 卡住但 CPU 低，請優先檢查 System Trace 中的「Off-core」時間。這是主執行緒在等待核心作業系統回傳資源的訊號。
4.  **檢查執行器競爭**：利用 Swift Executors Instrument 查看是否有太多並發任務擠在 Main Actor，這是目前開發者最容易忽略的效能殺手。
5.  **Data-Driven**：不要猜測效能瓶頸在哪，讓 Instruments 的 Flame Graph 或 Top Functions 的數據告訴你真相。

---
*註：本場演講推薦延伸觀看：*
*   *Optimize CPU Performance with Instruments (WWDC 2025)*
*   *Analyze Hangs with Instruments (WWDC 2023)*

<!-- resources -->

---

## 資源連結

- [Analyzing CPU profiles with call tree views](https://developer.apple.com/documentation/Xcode/analyzing-cpu-profiles-with-call-tree-views)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/268/4/7d94575d-e65b-4033-811f-199586ac587a/downloads/wwdc2026-268_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/268/4/7d94575d-e65b-4033-811f-199586ac587a/downloads/wwdc2026-268_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/268/
