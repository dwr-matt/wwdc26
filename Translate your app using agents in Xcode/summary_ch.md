# Translate your app using agents in Xcode

這是一份關於 WWDC 2026 Session「**Translate your app using agents in Xcode**」的技術摘要。

---

# 技術摘要：使用 Xcode Agents 進行應用程式在地化翻譯

## 概述
本 session 介紹了 Xcode 27 引入的全新自動化在地化（Localization）流程。開發者現在可以利用整合在 Xcode 中的 **Coding Agents**（AI 代理），針對應用程式中的字串進行大規模翻譯。該功能不僅能處理基礎翻譯，還能透過解析專案上下文、自動更新 `String Catalog` 以及預覽不同語言的 UI 佈局，大幅降低開發者進行多語言支援的門檻。

---

## 主要概念與機制

### 1. 核心自動化流程
Xcode 透過對話介面協助開發者完成在地化：
*   **專案準備**：Agent 會自動將目標語言加入專案設定，並編譯專案以確保所有可在地化字串被正確識別。
*   **字串收集**：自動在 `String Catalog` 中註冊字串。若尚未建立 Catalog，系統會自動生成 `Localizable.xcstrings` 或根據自訂表格名稱（如 `greetings.xcstrings`）進行分類。
*   **上下文感知（Context-Aware）翻譯**：不同於一般翻譯工具，Xcode 的 Agent 會將程式碼位置、變數名稱及類似術語的使用情況作為上下文提供給子代理（Sub-agents），以精確判斷如「Book」（書本 vs. 預約）等歧義詞彙。

### 2. 進階在地化功能
*   **複數與變體處理**：Agent 能夠正確識別並處理不同語言的複數變體（例如：英語的 `item` vs `items`，對應法語的 `un élément` vs `deux éléments`）。
*   **UI 佈局審查**：Agent 可協助渲染特定語言的 UI，以偵測文字長度差異（如法語通常比英語長）導致的截斷（Truncation）或佈局錯位問題。
*   **風格一致性**：透過分析現有翻譯，Agent 可確保新功能中的詞彙使用與舊有專案保持一致。

---

## 技術實踐與建議

### 1. 提供翻譯指引 (Translation Guidance)
為了避免 Agent 產生不符合產品調性的翻譯，開發者可以提供明確的規則：
*   建立一個名為 `translation.md` 的檔案。
*   內容包含：
    *   **詞彙表（Glossary）**：指定特定術語的翻譯（例如：將 "Landmark" 譯為 "Attrait"）。
    *   **排除清單（Exclusion List）**：定義不應被翻譯的內容（如產品名稱、商標）。
    *   **語氣定義（Tone of Voice）**：描述應用程式所需的語言風格（例如：專業、輕鬆、適合兒童等）。
*   Xcode 僅會在執行翻譯任務時讀取此檔案，以維持最佳效能。

### 2. 開發工作流建議
*   **保持字串的可在地化性**：持續使用 SwiftUI 的 `Text`、`Button` APIs，或在非 SwiftUI 程式碼中使用 `String(localized:)`。
*   **利用 TestFlight 獲取回饋**：人工在地化檢核不可或缺，請務必邀請母語使用者進行實機測試，收集截圖與翻譯修正建議。
*   **監控翻譯來源**：若透過匯出檔案與翻譯廠商協作，可查看 `Leveraged MT` 狀態標記，以辨識哪些翻譯是由 AI 代理所提供。

---

## 開發者實用重點 (Key Takeaways)

| 功能/策略 | 說明 |
| :--- | :--- |
| **一鍵在地化** | 直接在 Xcode 工具列呼叫 Agent，自動處理專案準備、字串收集與翻譯。 |
| **自動化 UI 測試** | 利用 Agent 檢查不同語言環境下的截斷（Truncation）與佈局問題，減少除錯時間。 |
| **知識對齊** | 使用 `translation.md` 作為單一來源，確保 AI 產出的術語符合品牌規範。 |
| **模型選擇** | 建議選擇具備長上下文窗口（Large Context Window）的模型，以處理跨檔案的大規模翻譯工作。 |

**總結：**
Xcode 27 的這項更新讓在地化不再是開發週期的最後一步，而是能融入開發流程的持續性任務。透過 Agent 的協助，開發者能更專注於產品的核心邏輯，並確保全球化體驗的一致性。

*(註：Session 中提及 `A bientot!`，鼓勵開發者立即嘗試在自己的專案中啟用此功能。)*

<!-- resources -->

---

## 資源連結

- [Localizing your app using agents](https://developer.apple.com/documentation/Xcode/localizing-your-app-using-agents)
- [Expanding Your App to New Markets](https://developer.apple.com/localization/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/213/4/be1ee662-a447-4df4-89a5-5411447c0eeb/downloads/wwdc2026-213_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/213/4/be1ee662-a447-4df4-89a5-5411447c0eeb/downloads/wwdc2026-213_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/213/
