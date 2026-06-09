# Build with the new Apple Foundation Model on Private Cloud Compute

這是一份為開發者整理的 WWDC 2026 Session **"Build with the new Apple Foundation Model on Private Cloud Compute (PCC)"** 技術摘要。

---

# 技術摘要：利用 Private Cloud Compute 構建 Apple Foundation Model 應用

## 概述
本 session 介紹了 Apple 推出的全新伺服器端大型語言模型（LLM），該模型運行於 Apple 的 **Private Cloud Compute (PCC)** 基礎設施上。開發者現在可以透過 Swift 的 `Foundation Models` 框架，在現有的應用程式中無縫調用這款更強大的伺服器模型，實現更複雜的推理、處理更大規模的輸入，並維持 Apple 一貫的隱私標準。

---

## 主要概念與框架介紹

### 1. 什麼是 Private Cloud Compute (PCC)？
PCC 是 Apple 專為處理複雜 AI 任務設計的伺服器運算架構。
*   **隱私保護：** 採用端到端隱私設計，用戶數據絕不會被存儲，僅用於當前的請求處理。經第三方研究人員驗證。
*   **無縫整合：** 與 iOS/macOS 系統及 iCloud 深度整合，開發者無需處理 API 金鑰或複雜的認證流程。
*   **成本效益：** 開發者無需支付 Token 費用。每個用戶擁有每日配額，用戶可透過 iCloud+ 升級配額。
*   **適用限制：** 目前開放給下載量少於 200 萬的 App 使用，需透過開發者網站申請。

### 2. PCC vs. On-Device 模型
| 特性 | On-Device 模型 | PCC 伺服器模型 |
| :--- | :--- | :--- |
| **連線需求** | 離線可用 | 需網路連線 |
| **Context Size** | 4K | 32K |
| **推理能力** | 基礎 | 支援 3 階段推理 (Light, Moderate, Deep) |
| **限制** | 無限制 | 有每日額度限制 |

---

## 程式碼模式與技術實作

### 簡單切換模型
`Foundation Models` 框架提供了一致的 Swift API。若你已經在使用 On-Device 模型，僅需更改一行程式碼即可切換到 PCC 模型：

```swift
// 原有的 On-Device 呼叫
let session = FoundationModel.onDevice.createSession()

// 切換至 PCC 模型 (語法一致)
let session = FoundationModel.privateCloudCompute.createSession()
```

### 推理 (Reasoning) 功能
PCC 模型支援推理，模型在生成最終回答前會進行「思考」。
*   **層級：** 支援 `light`、`moderate`、`deep` 三種推理模式。
*   **實作：** 在 `respond` 方法中設定推理等級。
*   **進度顯示：** 推理過程會產生額外的文字片段（包含在 transcript 中），建議監聽這些片段來呈現 UI 進度，特別是在 `deep` 模式下。

---

## 開發者實用重點與最佳實踐

### 1. 處理配額與限制
當使用者達到每日配額時，API 會拋出錯誤。開發者應優化 UI 體驗：
*   **檢查配額：** 檢查 `quota.usage` 狀態，不要使用單純的錯誤 Alert。
*   **行動化 UI：** 當使用者接近或達到上限時，應提供一條 UI 標籤（Label）以及一個引導按鈕，讓使用者能直接管理配額或升級 iCloud+。
*   **保持一致性：** 這些 UI 元件應長期存在於介面中，而非彈出視窗。

### 2. 使用 Xcode 進行模擬測試
開發者無需真的耗盡配額，可直接在 Xcode Scheme 中模擬狀態：
*   路徑：`Product` > `Scheme` > `Edit Scheme` > `Run` > `Options` > `Simulate Apple Foundation Models Availability`。
*   可模擬選項：`Quota Usage Limit Reached` (已達上限)、`Nearing Usage Limit` (即將達上限)。

### 3. 評估與優化
*   **評估框架 (Evaluations Framework)：** Apple 推出了全新的 `Evaluations Framework`，建議開發者利用它來判斷特定情境下應選擇 On-Device 還是 PCC 模型。
*   **模型混用：** 考慮將輕量任務交由 On-Device 模型（速度快、免費、離線），複雜任務交由 PCC 處理，以優化整體效能。
*   **檢查可用性：** 在呼叫 API 前，務必使用 Availability API 檢查使用者裝置是否支援 Apple Intelligence。

---

### 更多資源
*   **Meet the Evaluations Framework：** 學習如何科學地驗證模型表現。
*   **Build Agentic App Experiences with Foundation Models：** 深入了解如何將 On-Device 與伺服器模型整合應用。
*   **Debug and Profile Agentic App Experiences with Instruments：** 使用 Instruments 監控運行時模型行為。

<!-- resources -->

---

## 資源連結

- [Adding server-side intelligence with Private Cloud Compute](https://developer.apple.com/documentation/FoundationModels/adding-server-side-intelligence-with-private-cloud-compute)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/319/4/1a3ac4f6-73d2-4a24-9e5d-0cfd56564f42/downloads/wwdc2026-319_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/319/4/1a3ac4f6-73d2-4a24-9e5d-0cfd56564f42/downloads/wwdc2026-319_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/319/
