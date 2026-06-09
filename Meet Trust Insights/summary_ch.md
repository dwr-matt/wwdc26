# Meet Trust Insights

這是一份關於 WWDC 2026 Session「Meet Trust Insights」的詳細技術摘要，旨在幫助開發者快速掌握如何整合這項新安全功能。

---

# 技術摘要：利用 Trust Insights 檢測社交工程威脅

## 概述
隨著「社交工程（Social Engineering）」攻擊手法日益精密，攻擊者常利用心理操縱（如技術支援詐騙、假冒權威、親友緊急詐騙等）引導用戶進行「看似合法」的操作。傳統的生物辨識與多重身分驗證無法偵測此類惡意行為，因為操作是由用戶本人在被脅迫下執行的。

**Trust Insights** 是 iOS 27 推出的全新框架，旨在透過分析「行為脈絡（Behavioral Context）」來識別用戶是否處於被操控或受限的狀態，協助開發者在不侵犯隱私的前提下，識別並防禦潛在的惡意 coercion（強制/操縱）行為。

---

## 主要概念與 API 架構

### 1. 核心功能
*   **非侵入式偵測：** 透過系統級 ML 模型分析互動模式、時序與上下文。
*   **隱私保護：** 本地裝置來源數據絕不離開裝置，處理後僅回傳單一風險評級。
*   **行為上下文：** 系統能辨識用戶是否在「即時指導（Real-time Coaching，如語音通話指導）」下操作。

### 2. 操作分類 (Operation Categories)
開發者在請求評估時，需指明當前的操作類型，以利模型判斷：
*   **Payment：** 資金、資產轉移或遊戲內購買。
*   **Account：** 修改帳號詳情或安全性設定。
*   **Resource Use：** 高成本資源調用（如 AI 推理）。
*   **Communication：** 發送訊息、提交表單或簽署文件。
*   **Other：** 不屬於上述類型的其他操作。

---

## 整合步驟與技術模式

### A. 配置與權限
在 Xcode 中宣告 `Trust Insights` Capability，並在程式碼中確認使用者是否授權該 API 使用。

### B. 發起評估 (Evaluation Flow)
1.  **建立參數包：** 定義評估的 Schema 與模型版本。
2.  **建立評估器：** 設定 `InsightContext` 與操作分類。
3.  **異步請求：** 呼叫 `requestEvaluation`，需注意此過程需要網際網路連線，且可能需幾秒鐘時間。

```swift
// 示意程式碼模式
let context = InsightContext(category: .payment)
let evaluator = InsightEvaluator(context: context)

// 確認授權後發起評估
Task {
    let result = try await evaluator.requestEvaluation()
    handle(result)
}
```

### C. 結果解析
評估結果分為三個層級：
1.  **Unknown：** 無證據顯示風險（不代表完全安全）。
2.  **Medium：** 偵測到部分風險，建議增加摩擦（Friction）或額外驗證。
3.  **High：** 明顯的詐騙風險，建議暫停操作並告知用戶。

---

## 開發者的實用重點（Best Practices）

1.  **回饋迴圈 (Feedback Loop) 是義務：**
    *   **即時回饋：** 必須呼叫 `ReportConsumption` 回報應用程式如何處置結果（如：減少摩擦、增加摩擦、未做處理等）。若未回報，應用程式將面臨流量限流（Rate Limiting）。
    *   **離線回饋：** 若事後證實為詐騙，透過 Apple Business Register 提交離線標籤，協助訓練模型。

2.  **設計決策邏輯：**
    *   **切勿單一依賴：** Trust Insights 應作為風險引擎的「參考因子」之一，不建議僅因評級結果就完全阻斷用戶操作。
    *   **用戶體驗平衡：** 利用現有的動畫或 Interstitial 畫面來填補評估所需的等待時間。

3.  **開發測試：**
    *   使用 Xcode 的 Build Schemes 自訂啟動參數（Launch Arguments），模擬不同的評估結果與錯誤情境，以測試 UX 流程。

4.  **隱私聲明：**
    *   此框架絕不會分析照片、訊息或郵件內容。
    *   若用戶在系統設定中關閉 Trust Insights，應注意系統可能會啟動「冷卻期（Cooldown period）」以保護可能正受他人操控的用戶。

5.  **錯誤處理：**
    *   務必獨立處理「評估層級錯誤」與「洞察層級錯誤」。
    *   **重要提示：** 若 API 回傳 `Unknown` 或缺失值，絕對不能視為低風險，應採取保守的預設行為。

---

**總結建議：** Trust Insights 是一項強大的生態系統工具。開發者應儘早將其整合進高風險業務邏輯（如大額支付），並透過「回饋迴圈」與 Apple 共同建立更安全的 App 生態。

<!-- resources -->

---

## 資源連結

- [TrustInsights](https://developer.apple.com/documentation/TrustInsights)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/379/4/e12c4703-5c00-44f7-a5f8-80f6e5b7ebd5/downloads/wwdc2026-379_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/379/4/e12c4703-5c00-44f7-a5f8-80f6e5b7ebd5/downloads/wwdc2026-379_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/379/
