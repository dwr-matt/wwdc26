# Dub Dub Daily: Day 3

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 第三天重點內容，特別聚焦於 Apple Intelligence 的生態系整合。

---

# WWDC 2026 技術摘要：Dub Dub Daily Day 3
**焦點：Apple Intelligence 與開發者工具的整合**

## 概述
本場次深入探討了 Apple 如何將人工智慧（Apple Intelligence）深度整合至開發者生態中。重點在於如何透過「Foundation Models Framework」、「App Intents」以及全新的「Dynamic Profiles」技術，讓開發者的 App 成為 Apple Intelligence 系統的一部分。核心理念在於「**讓 App 因智慧而更強大，讓智慧因 App 而更具意義**」。

---

## 主要概念與框架介紹

### 1. Foundation Models Framework (基礎模型框架)
此框架於去年推出，旨在讓開發者在 App 中運用 Apple 的裝置端（On-device）模型。今年更新的亮點包括：
*   **私有雲端運算 (Private Cloud Compute)**：開發者現在可以存取驅動 Apple Intelligence 的強大伺服器模型。
*   **第三方模型支援**：整合了 Gemini 與 Anthropic 的 Claude，提供開發者更多元的選擇。
*   **Vision Framework 整合**：現在 Foundation Models Framework 可直接呼叫視覺工具（如 OCR 與條碼掃描），讓模型處理影像數據更加無縫。

### 2. Dynamic Profiles (動態配置)
這是一項關鍵的架構變更，讓開發者能根據使用場景（Context）決定邏輯路徑：
*   **彈性路徑選擇**：開發者可定義哪些請求應留在裝置端（注重隱私、離線需求），哪些請求應送往私有雲端或第三方模型（需要更高算力）。
*   **技能封裝 (Skills as a package)**：開發者可以採用現成的技能模組，或自訂抽象的「技能」邏輯，取代過去單一的指令集。

### 3. App Intents (應用程式意圖)
這是 App 與 Siri 進行深度互動的橋樑。
*   **功能**：讓使用者透過自然語言（Natural Language）對 App 下達指令，查詢隱藏在 App 內部的資訊，或是執行具體操作（如：設定鬧鐘、更新日曆、查詢特定資訊）。
*   **整合關鍵**：開發者需採用 Apple 定義的「App Intents Schemas」，將內容與功能「曝露」給系統層級的 Siri，達成跨 App 的智慧運作。

---

## 程式開發與技術模式

### 動態路徑邏輯模式
開發者不再需要為所有任務設定單一的提示（Prompt）或模型路徑。透過 Dynamic Profiles，開發者可以實踐以下模式：
1.  **判定階段**：在 App 層級判定請求的複雜度與隱私需求。
2.  **分流處理**：
    *   `if request.isPrivate && request.isLowCompute { executeOnDevice() }`
    *   `else { executeViaPrivateCloudOrThirdParty() }`
3.  **統一接口**：儘管後端運算路徑不同，透過 Foundation Models Framework，前端開發者可以使用一致的 API 介面進行呼叫。

---

## 開發者實用重點（Key Takeaways）

*   **系統級整合的重要性**：採用 App Intents 不僅是功能提升，更是讓您的 App 進入 Apple Intelligence 生態系的必要門票。透過 Schema 設計，讓 Siri 成為您 App 的互動介面。
*   **混合式架構**：善用裝置端模型（隱私、延遲敏感）與雲端模型（複雜推理、高算力）的結合，能為使用者打造更流暢的體驗。
*   **即時資源**：
    *   **文件與參考資料**：所有相關 API 文檔與範例程式碼已更新至 [developer.apple.com/documentation](https://developer.apple.com/documentation)。
    *   **專家諮詢**：Apple 工程師正在官方論壇（Developer Forums）全天候回答關於新技術的疑難雜症。
    *   **互動式學習**：參與 Group Labs 線上研討會，若錯過直播，可透過 Apple Developer App 查看隨選（On-demand）錄影。

---
*編輯建議：開發者應優先檢視 App Intents 的 Schema 定義，這將是提升 App 在 iOS 系統中「被發現率」與「可用性」的關鍵第一步。*

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/398/2/410c7536-3689-45c3-a343-661e3cdd641f/downloads/wwdc2026-398_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/398/2/410c7536-3689-45c3-a343-661e3cdd641f/downloads/wwdc2026-398_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/398/
