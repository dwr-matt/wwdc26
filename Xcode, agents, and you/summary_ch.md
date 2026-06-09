# Xcode, agents, and you

這是一份關於 WWDC 2026 Session「**Xcode, agents, and you**」的詳細技術摘要。

---

# WWDC 2026 技術摘要：Xcode, Agents, and You

## 概述
本場 Session 介紹了 Xcode 27 在 AI Agent（智慧代理）整合方面的重大革新。Xcode 27 不再僅僅是程式碼編輯器，而是演進為一個能夠理解專案上下文、執行多步驟任務、與開發者共同協作的「架構師」。透過這些代理工具，開發者可以從繁瑣的 boilerplate 程式碼與探索性任務中解脫，專注於功能設計與視覺呈現。

---

## 主要概念與工具介紹

### 1. 代理互動模式 (Agent Interaction)
*   **探索模式 (Exploration)：** 透過 `Option + Shift` 在工具列點擊即可開啟新對話。Agent 可讀取整個專案結構、build 設定與開啟的檔案，提供架構剖析與路徑建議。
*   **計畫模式 (Plan Mode)：** 使用 `/plan` 指令進入，將開發過程「對話化」。開發者在寫入程式碼前，先與 Agent 確立架構戰略，確保功能基礎穩固。
*   **排隊訊息 (Queued Messages)：** 開發者可以在 Agent 執行當前任務時，預先排入後續指令，創造緊密的溝通循環。
*   **Artifacts (產出物)：** 介面分為左側（對話紀錄）與右側（Artifacts）。Artifacts 包含產生的檔案、程式碼 diff 與即時預覽，讓開發者隨時審視變更。

### 2. 進階整合能力
*   **Apple Document Search：** 當 Agent 遇到不熟悉的框架（如 SwiftData 或 SwiftUI Table），會自動調用 Apple 官方文件，確保產出的程式碼符合最新 API 標準與最佳實踐。
*   **自我修正迴圈 (Self-Correction Loop)：** 若編譯失敗，Xcode 會將錯誤訊息直接反饋給 Agent，讓 Agent 自行迭代修正並重新嘗試編譯與預覽。
*   **平行任務協調 (Orchestration)：** 使用子代理 (Sub-agents) 同步處理多項任務（如同時進行在地化翻譯與無障礙標籤添加），大幅提升大型重構或功能導入的速度。

---

## 示範技術與開發模式

### 視覺化原型設計
*   **Sketch-to-Code：** 開發者可利用 iPad（如 Freeform）繪製 UI 草圖，並將草圖發送給 Agent 作為設計藍圖。
*   **Inline Annotations (內聯註解)：** 在程式碼行直接添加註解（例如：「在此處加入淡入動畫」、「調整趨勢線顏色」）。Agent 會將註解周圍的程式碼視為上下文，精準修改特定區塊。

### 知識庫維護
*   **自動化文件生成：** Agent 可根據專案結構自動產生架構文件（Architecture Docs），並直接存入專案目錄中，讓團隊成員能快速上手。此知識庫為動態產物，隨程式碼更新而進化。

---

## 開發者的實用重點

1.  **從「寫程式」轉向「指揮架構」：** 開發者的核心價值在於定義願景（Vision）。利用 Agent 進行繁雜的 API 研究與 Boilerplate 撰寫，開發者應將時間投資在「Plan Mode」的規劃階段與 Code Review。
2.  **善用工具驗證：** 不要只依賴 Agent 產出的文字，務必活用 Xcode 27 的 **Build、Preview 與 Test** 整合工具。Agent 可以自動生成測試案例，應確保將這些測試併入現有的測試套件中。
3.  **精確指引：** 當調整 UI 細節時，善用圖像附件與 inline annotations。提供具體的視覺參考或程式碼區塊連結，遠比單純的文字描述更能產出高品質的 UI。
4.  **擁抱自動化流程：** 對於在地化（Localization）與無障礙（Accessibility）等標準化流程，直接交付給 Agent 進行全域配置，並利用子代理協調機制，能有效減少重複性勞動。

---

## 下一步學習資源
*   **Xcode 27：** 立即下載並在現有專案中嘗試 Agentic workflows。
*   **推薦觀看相關 Session：**
    *   *Create UI Prototypes Using Agents in Xcode*（深入了解 UI 原型開發技巧）
    *   *Translate Your App Using Agents in Xcode*（深入了解自動化在地化流程）

--- 
*編輯備註：Xcode 27 提供的「知識庫文件」功能是維護大型專案團隊一致性的強大工具，強烈建議開發者在導入新功能時，養成讓 Agent 同步更新文件架構的習慣。*

<!-- resources -->

---

## 資源連結

- [Writing code with intelligence in Xcode](https://developer.apple.com/documentation/Xcode/writing-code-with-intelligence-in-xcode)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/259/4/f4d40bb5-32db-418f-8a6e-396c77044afb/downloads/wwdc2026-259_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/259/4/f4d40bb5-32db-418f-8a6e-396c77044afb/downloads/wwdc2026-259_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/259/
