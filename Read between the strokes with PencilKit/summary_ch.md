# Read between the strokes with PencilKit

這是一份針對 WWDC 2026 Session「Read between the strokes with PencilKit」的技術摘要，旨在協助開發者快速掌握 iOS 27 中 PencilKit 的重大更新。

---

# WWDC 2026 技術摘要：Read between the strokes with PencilKit

## 概述
本 session 介紹了 iOS 27 中 PencilKit 的一系列重量級更新。最核心的變革在於原生「手寫辨識 (Handwriting Recognition)」功能的開放，讓開發者能輕易地將手寫內容轉化為可搜尋、可互動的文字。此外，本屆更新還大幅增強了對繪圖資料模型（Data Model）的存取能力，包含路徑轉換、筆畫識別、以及強大的筆畫切割（Slicing）與子筆畫提取功能，為自訂繪圖應用程式提供了更深層的控制權。

---

## 主要概念與 API 介紹

### 1. 手寫辨識 (Handwriting Recognition)
透過 `PKStrokeRecognizer`，開發者現在可以將 PencilKit 的筆畫資訊轉換為文字。
*   **離線與隱私**：辨識完全在裝置端進行，無需聯網，支援 29 種語言。
*   **關鍵 API**：
    *   `PKStrokeRecognizer`：核心類別，負責解譯筆畫。
    *   **Indexable Content**：提供繪圖內容的字串表示，適合用於 Spotlight 搜尋，並會自動包含語意模糊時的多種候選結果。
    *   **Search API**：根據目標字串返回搜尋結果陣列，並可與 `UIFindInteraction` 整合，實現系統級的「尋找與取代」互動體驗。
*   **無障礙支援**：透過辨識結果，開發者能讓 VoiceOver 朗讀手寫內容，縮短手寫與打字文字之間的無障礙差距。

### 2. 路徑轉換 (Path Conversion)
PencilKit 使用「三次均勻 B 樣條 (Cubic Uniform B-splines)」儲存路徑，iOS 27 新增了轉換機制：
*   **Bezier 互轉**：支援 `PKStrokePath` 與標準 Bezier 路徑的相互轉換。
*   **用途**：即便開發者使用自訂的 Canvas 繪圖引擎（非 `PKCanvasView`），也能將既有的 Bezier 筆畫轉換為 `PKStrokePath`，從而使用 PencilKit 的手寫辨識功能。

### 3. 繪圖模型與筆畫控制
*   **Stable Identity (Identifiable)**：`PKStroke` 與 `PKStrokePath` 現在遵循 `Identifiable` 協議。提供穩定的唯一識別碼 (UID)，方便在 Undo/Redo 或編輯操作中持續追蹤特定筆畫。
*   **Selection API**：新增 `canvasViewSelectionDidChange` 回呼，可精準捕捉用戶的選取狀態變更。
*   **Render Group ID**：可以控制不同筆畫是否要進行「濕筆（Wet Ink）」的混合渲染。

### 4. 筆畫切割與提取 (Stroke Slicing)
*   **程式化抹除 (Programmatic Erasing)**：允許開發者模擬橡皮擦工具，透過傳入 `PKStrokePath` 來切割筆畫，將原本的一筆切分為多個帶有獨立 Mask 的筆畫。
*   **子筆畫提取 (Sub-stroke Extraction)**：透過下標 (Subscript) 語法配合參數化範圍（Parametric Range），可以精準擷取筆畫的特定片段。這對於動畫回放、檢查筆順（如書寫漢字練習）非常實用。

---

## 開發者實用重點與注意事項

1.  **效能管理**：
    *   **非同步處理**：手寫辨識雖快，但若在處理極複雜繪圖或頻繁呼叫切割 API 時，建議於背景執行緒處理，避免阻塞 UI。
    *   **資源消耗**：不要對「每一個筆畫」觸發即時索引更新，應建立合理的快取與更新策略。
2.  **模擬器限制**：Xcode 模擬器目前僅支援 Latin（拉丁）字元的手寫辨識，測試多國語言（如中文）需在實體裝置上進行。
3.  **索引正確性**：若將索引內容存入硬碟，需注意辨識模型會隨 OS 更新而提升精度，建議在讀取時比較版本，必要時重新索引。
4.  **與 PaperKit 的關聯**：iOS 26 推出的 PaperKit 是建立在 PencilKit 之上的系統級體驗，本 session 所述的所有 API 均同時適用於 PaperKit。

---

## 總結
iOS 27 的 PencilKit 不再僅僅是繪圖工具，它透過開放手寫辨識與底層模型存取，轉變為一個高度可編程的互動框架。對於開發「教育」、「生產力」或「筆記」類應用的開發者來說，這些 API 提供了極高的開發彈性與系統整合能力。

*建議參閱：WWDC20 的「Inspect, Modify, and Construct PencilKit drawing」與 WWDC25 的「Meet PaperKit」以建立完整知識體系。*

<!-- resources -->

---

## 資源連結

- [Controlling stroke rendering for animation and editing](https://developer.apple.com/documentation/PencilKit/controlling-stroke-rendering-for-animation-and-editing)
- [Recognizing handwriting and converting it to text](https://developer.apple.com/documentation/PencilKit/recognizing-handwriting-and-converting-to-text)
- [Building a handwriting recognition experience with PencilKit](https://developer.apple.com/documentation/PencilKit/building-a-handwriting-recognition-experience-with-pencilkit)
- [PencilKit](https://developer.apple.com/documentation/PencilKit)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/203/4/eb979cd5-af5b-4091-87ec-4839e8d131b9/downloads/wwdc2026-203_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/203/4/eb979cd5-af5b-4091-87ec-4839e8d131b9/downloads/wwdc2026-203_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/203/
