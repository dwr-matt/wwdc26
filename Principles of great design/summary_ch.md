# Principles of great design

這是一份針對 WWDC 2026 Session「Principles of great design」的詳細技術摘要。

---

# 技術摘要：Principles of Great Design (WWDC 2026)

## 概述
本場次由 Apple 設計佈道師 Linda 與 Doug 主講，旨在探討「設計」在 Apple 軟體開發中的核心定義：**「設計是帶有目的性的創造（Design is making something with intention）」**。講者強調設計不僅僅是視覺或行為，而是如何透過「專注於對使用者最重要的價值」來打造產品。本議程梳理了六大 foundational principles（基本設計原則），為開發者提供了一套判斷產品決策的思維框架。

---

## 主要概念與核心設計原則

Apple 提出的六大設計原則，旨在協助開發者在開發過程中做出更具「目的性」的決策：

1. **目的性 (Purpose)**：在編寫任何程式碼前，先自問產品是否具備明確目標。設計決策往往在於「決定哪些功能不該加入」，以避免浪費使用者的時間與信任。
2. **自主性 (Agency)**：賦予使用者控制權，而非預設好所有路徑。
    *   **寬容性 (Forgiveness)**：提供「復原（Undo）」操作，對於毀滅性的操作應進行二次確認，但在一般流程中應減少中斷。
3. **責任感 (Responsibility)**：這是一個產品能獲得信任的關鍵。
    *   **隱私保護**：不要在使用者未了解應用程式價值前就拋出權限請求（Permission Prompts）。
    *   **AI 倫理與安全**：在導入 AI 功能時，必須預期模型可能產生的不準確與風險，並加入適當的緩衝機制（如預覽、確認、過濾）。
4. **熟悉感 (Familiarity)**：利用使用者既有的經驗與認知。
    *   **隱喻 (Metaphor)**：使用大眾熟悉的視覺符號（如垃圾桶代表刪除），但需注意隱喻不宜過於抽象或過於字面化。
    *   **一致性 (Consistency)**：相同的操作在不同螢幕間應具備相同的行為與位置，減少使用者的學習成本。
5. **靈活性 (Flexibility)**：承認使用者的多樣性。
    *   考量不同的使用情境（如：手機上的觸控 vs. Mac 上的精準指標操作）。
    *   支援無障礙功能，並允許使用者根據個人偏好進行客製化。
6. **簡潔性 (Simplicity)**：並非指「視覺極簡」，而是指「去除冗餘，保留核心」。
    *   **簡明 (Concise)**：使用直白語言，減少專業術語。
    *   **清晰 (Clear)**：透過層級（Hierarchy）、排版與對比，引導使用者關注重點。
7. **工藝精神 (Craft)**：這是對細節的極致追求。良好的工藝能建立使用者的信心。包括：高品質的字體、適應性的色彩配置、順暢的動畫反饋。

---

## 關鍵技術設計模式

雖然本議程非深度的程式碼教學，但強調了以下幾種值得開發者落實的設計模式：

*   **請求權限的時機點 (Timing of Permission)**：
    *   不要在 App 啟動時立即詢問權限，應在使用者實際執行需要該功能的動作時，給予完整的上下文（Context）說明後再詢問。
*   **針對 AI 功能的防禦性設計**：
    *   在處理關鍵數據（如健康、過敏源）時，必須預判模型輸出的不確定性，並強制加入「人工審核」或「警示預覽」機制。
*   **階層化呈現 (Hierarchical Clarity)**：
    *   使用「順序」、「間距」與「對比」來引導使用者視線。針對複雜數據，考慮將其視覺化為圖表，而非僅僅羅列文字。
*   **一致性的導航與操作**：
    *   遵守平台規範，維持 UI 元素的預測性（例如 Mac 視窗的關閉按鈕永遠在左上角），不要為了「創意」而打亂通用習慣。

---

## 開發者實用重點 (Takeaways)

*   **設計是投資，而非點綴**：Delight（愉悅感）不是在專案結尾加上 confetti 動畫，而是所有設計原則落實後的自然成果。
*   **定期檢視產品生命週期**：良好的設計具有長遠性（Longevity），當 Apple 發布新硬體或新功能時，開發者應主動探索這些變更是否能提升 App 的使用體驗。
*   **善用 Human Interface Guidelines (HIG)**：
    *   HIG 是所有 Apple 平台設計的黃金準則。
    *   建議開發者前往 Apple 官網查看新更新的 **「Design Principles」頁面**，深入研究這些原則如何應用於實際開發中。
*   **捨棄的勇氣**：當某個功能的潛在風險大於其價值時，最好的決定往往是「移除」該功能，而不是試圖修補它。

---
*編輯建議：建議開發者在下一次的 Sprint Planning 中，將這些原則列入 UX 檢視清單，特別是在評估 AI 功能與權限需求時使用。*

<!-- resources -->

---

## 資源連結

- [Human Interface Guidelines: Design principles](https://developer.apple.com/design/human-interface-guidelines/design-principles)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/250/4/ad804f32-2805-48aa-891c-8c742579acab/downloads/wwdc2026-250_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/250/4/ad804f32-2805-48aa-891c-8c742579acab/downloads/wwdc2026-250_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/250/
