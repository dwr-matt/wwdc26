# Craft clear names for features and labels in your app

這是一份關於 Apple WWDC 2026 Session **"Craft clear names for features and labels in your app"** 的技術摘要。

---

# 摘要：如何為應用程式的功能與標籤命名

## 概述
命名不僅僅是標記功能，它更是使用者體驗（UX）的核心組成部分。本 session 強調，良好的命名能直接影響使用者對應用程式的信任感與歸屬感。主講人通過 Apple 現有的產品案例，分析了如何運用一致的判斷標準，將複雜的功能轉化為直觀、易懂的標籤，讓開發者與設計者能建立一套屬於自己 App 的「語言體系」。

---

## 主要概念：命名的三大判斷標準
在進行命名時，講者提出了三個核心標準作為引導原則，這些標準並非死板的規則，而是幫助決策的框架：

1.  **歸屬感 (Belongs)：**
    *   名稱是否與 App 的整體風格、上下文一致？
    *   該名稱在選單、分頁欄或設定中是否與其他項目和諧共存？
2.  **預期 (Expectations)：**
    *   使用者讀到名稱時，是否能準確預測點擊後的行為？
    *   名稱是否能建立信任（避免過於模糊或帶有審判意味的用詞，例如將金額標示為「spending power」而非「balance」）。
3.  **通用性 (Works Everywhere)：**
    *   該名稱是否能跨越語言、市場及不同平台（iOS, macOS, visionOS 等）保持一致且清晰。

---

## 命名設計的實踐流程
講者建議開發團隊在命名時，應採取以下步驟：

### 1. 釐清受眾與場景
不要從「功能是什麼」（技術視角）出發，要從「對使用者有什麼好處」（體驗視角）出發。
*   **練習步驟：** 製作便利貼，針對該功能回答以下三個問題：
    *   **Think (思考)：** 使用者會想到什麼？（如：簡潔、高效）
    *   **Feel (感受)：** 使用者應該有什麼感覺？（如：安心、懷舊、隱私受保護）
    *   **Do (執行)：** 使用者點擊後能做到什麼？（如：分享、儲存）

### 2. 主題分組與測試
*   將蒐集到的詞彙根據主題進行分組（如：便利性、安全性）。
*   **口語測試法 (Gut Check)：** 將候選名稱放入真實情境句型中，例如：「Hey, check out [Name]」或「Just search for [Name]」。如果聽起來不自然或像在讀說明書，則該名稱不合格。

---

## 案例研究與技術邏輯

*   **Apple Cash (Balance vs. Spending Power)：** 
    *   *Spending Power* 聽起來模糊且帶有評價性（若餘額為零會造成負面觀感）。
    *   *Balance* 是業界標準，中性且清晰，能建立最高程度的信任。
*   **Apple Podcasts (Enhanced Dialogue)：**
    *   功能是「隔離人聲」。若命名為 *Vocal Isolation* (技術術語) 或 *Enhanced Playback* (對象不明)，皆不夠精準。
    *   *Enhanced Dialogue* 同時解決了「什麼被增強」(Dialogue) 以及「誰受惠」(使用者)，且與 Apple TV 的命名統一，強化了產品的歸屬感。
*   **Apple Music (Auto Mix)：**
    *   有時創造新詞是可行的，前提是該詞彙能透過其構成部分（Auto + Mix）被直觀理解，無需額外解釋。

---

## 開發者的實用重點

1.  **品牌與清晰度的權衡：**
    *   不要為了「聰明」或「創意」而犧牲清晰度。如果是複雜功能，優先選擇描述性強的名稱；若是情感連結強的功能（如 Photos 的 Memories），則可選擇具備意象的名稱。
2.  **以人為本，而非以技術為本：**
    *   避免直接暴露演算法邏輯或技術參數。詢問自己：「這是使用者在生活中會使用的語言嗎？」
3.  **命名是長期積累的語言：**
    *   每一個良好的命名都會讓下一個功能命名變得更簡單。當你的 App 擁有一套一致的術語時，使用者會感到更「在家」(at home)。
4.  **動詞的力量：**
    *   對於控制類功能（如設定項），考慮使用「動詞」開頭（例如：*Isolate* vocals），這能讓使用者感受到操作的主動權。

---
**總結：** 好的命名能降低學習曲線，提升用戶滿意度。在下次開發功能時，請務必將「名稱」視為 UI 的第一道防線。

<!-- resources -->

---

## 資源連結

- [Human Interface Guidelines: Writing](https://developer.apple.com/design/human-interface-guidelines/writing)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/290/4/6a4ef6cd-2a95-432c-aac9-315cb3cb7ff6/downloads/wwdc2026-290_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/290/4/6a4ef6cd-2a95-432c-aac9-315cb3cb7ff6/downloads/wwdc2026-290_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/290/
