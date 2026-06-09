# Secure your app: mitigate risks to agentic features

這是一份針對 WWDC 2026 Session「**Secure your app: mitigate risks to agentic features**」的技術摘要。

---

# 技術摘要：為你的 App 代理功能（Agentic Features）建立安全防護

本 Session 旨在探討如何在 Apple 平台的架構下，識別並緩解代理型應用程式（Agentic Apps）面臨的新興安全風險。隨著大語言模型（LLM）被整合為系統關鍵組件，App 獲得了自動執行任務的能力，但也因此引入了如「間接提示注入」（Indirect Prompt Injection）等潛在威脅。

## 1. 主要安全風險概念

講者將代理系統的風險歸納為幾個核心面向，開發者在進行威脅建模（Threat Modeling）時應特別注意：

*   **間接提示注入（Indirect Prompt Injection）：** 當模型處理的「外部上下文」（如行事曆事件、朋友貼文）中嵌入了惡意指令，試圖控制模型的行為路徑時，就會發生此攻擊。
*   **資料中毒（Data Poisoning）：** 攻擊者操縱模型執行的參數。例如：原本要傳訊息給媽媽，卻被注入指令改傳給攻擊者。
*   **動作中毒（Action Poisoning）：** 攻擊者誘導 LLM 執行非預期的操作。例如：誘導模型打開惡意網站而非總結 Email。
*   **「致命三要素」（Lethal Trifecta）：** 當系統同時具備以下三者時風險最高：
    1. 存取私有資料。
    2. 接觸不可信任的內容（如網路爬取內容）。
    3. 具備外部溝通或執行副作用（Side-effect）動作的能力。

## 2. 核心框架與 API 介紹

本場次介紹了兩大框架的安全防護機制：

### A. Foundation Models Framework
提供更細粒度的控制，適合自行設計 Agent 的開發者。
*   **Lifecycle Event Modifiers：** 透過事件修飾符在 Agent 執行的關鍵生命週期點進行攔截，實現確定性的安全檢查。

### B. App Intents Framework
適合將 App 功能整合至 Siri 或系統級 AI 的開發者。
*   **風險評估系統：** 系統會根據 Intent 的「風險元數據」（Risk Metadata）及當下「動態系統狀態」，自動判斷是否需要使用者確認（Confirmation）。
*   **鎖定畫面驗證（Lock Screen Authentication）：** 對於具有破壞性或高風險的 Intent，可強制要求解鎖裝置方可執行。

## 3. 示範程式碼與技術模式

### 使用 Lifecycle Event Modifiers 攔截工具呼叫
在 `Foundation Models` 中，透過 `on-tool-call` 修飾符，開發者可以在工具執行前攔截，並強制要求使用者確認：

```swift
// 在設定 Profile 時注入安全政策
profile.onToolCall { toolCall in
    // 檢查是否為高風險工具 (例如：轉帳、下單)
    if toolCall.name == "order_tea" {
        // 若使用者未確認，拋出錯誤以阻止工具執行
        guard try await userConfirm() else {
            throw SecurityError.userDenied
        }
    }
}
```

### 使用 History Transform 進行防護
在將數據傳送給模型前，透過 `history transform` 進行預處理，如「標記（Spotlighting）」或「脫敏（Redacting）」：

```swift
// 在傳送給 LLM 前標記不可信資料
profile.historyTransform { transcript in
    transcript.entries.forEach { entry in
        if entry.isFromUntrustedSource {
            // 將內容包裹在 <untrusted> 標籤中，提醒模型注意
            entry.content = delimit(entry.content, tag: "untrusted")
        }
    }
}
```

### 設定 App Intent 的權限與安全性
對於 `App Intents`，開發者可以明確定義鑑權政策：

```swift
struct DeletePhotoIntent: AppIntent {
    // 強制要求鎖定畫面驗證，防止惡意執行
    static var authenticationPolicy: AuthenticationPolicy = .requiresAuthentication
    
    // ... 其他實作
}
```

## 4. 開發者實用重點與總結

1.  **威脅建模先行：** 識別所有「非受控」的資料輸入源（如來自網路的行事曆、訊息），並將其視為攻擊面。
2.  **優先採用確定性（Deterministic）防護：** 相比於依賴模型的安全性（機率性），在程式碼層面強制執行確認檢查（Confirmation）或輸入過濾（Redaction/Spotlighting）更為可靠且可審計。
3.  **副作用管理：** 任何涉及金錢、資料刪除、發送訊息的 Intent，都應嚴格審視其風險等級，必要時手動提高認證需求。
4.  **鎖定畫面安全：** 永遠假設「鎖定畫面」可能有物理攻擊者，對於涉及敏感資料的操作，務必將 `authenticationPolicy` 設定為 `.requiresAuthentication`。

**總結建議：** 構建代理應用時，開發者不僅要考慮「模型能做什麼」，更要考慮「當惡意內容混入時，模型會做什麼」。透過 Foundation Models 的生命週期鉤子與 App Intents 的自動風險評估，能大幅降低應用遭受攻擊的風險。

<!-- resources -->

---

## 資源連結

- [Security Overview](https://developer.apple.com/security/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/347/4/07cdbfeb-280a-49e3-aeba-c18fbb0d32b4/downloads/wwdc2026-347_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/347/4/07cdbfeb-280a-49e3-aeba-c18fbb0d32b4/downloads/wwdc2026-347_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/347/
