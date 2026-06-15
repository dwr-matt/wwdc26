# Create high-quality images using Image Playground

這份技術摘要旨在幫助開發者快速掌握 WWDC 2026 中關於 **Image Playground** 框架的核心應用。

---

# WWDC 2026 技術摘要：使用 Image Playground 創建高品質影像

## 概述
本 Session 介紹了全新的 `ImagePlayground` 框架，旨在讓開發者能夠將 Apple 的生成式 AI 影像模型直接整合進 App 中。該框架提供了一套高度一致的 UI 與 API，支援多種風格（如動畫、插畫、素描等）的影像生成。所有運算皆透過 Apple 的 **Private Cloud Compute** 進行，確保隱私並無需開發者維護後端基礎設施。

## 主要概念與 API
Image Playground 徹底取代了舊有的 `Image Creator` API。其核心優勢在於：
*   **零後端維護**：不需要 API Key、伺服器端點或額外的資源配置。Apple 處理了所有的使用限制（Usage Limits）與 UI 提示。
*   **原生體驗**：提供與系統應用（如訊息、Freeform）一致的介面。
*   **靈活的輸入上下文**：開發者可以將 App 內的資訊（如主題描述、現有圖片、PencilKit 繪圖）傳遞給模型，作為生成的「種子（Seed）」。
*   **私密運算**：所有生成任務均由 Private Cloud Compute 執行，確保使用者隱私。

## 關鍵技術與 API 模式

### 1. 快速整合 (SwiftUI)
開發者只需透過 View Modifier 即可呼叫介面，無需手動建立初始化邏輯：
```swift
.imagePlaygroundSheet(isPresented: $isShowing) { result in
    // 處理回傳的臨時 URL
    let imageUrl = result.url
}
```

### 2. 傳遞上下文 (Concepts)
為了讓模型更精確地符合 App 場景，可以使用 `ImagePlaygroundConcept`：
*   **文字概念**：`ImagePlaygroundConcept.text("Birthday celebration")`
*   **視覺概念**：`ImagePlaygroundConcept.drawing(pkDrawing)` (整合 PencilKit)
*   **參考圖**：在 sheet 開啟時傳入 `sourceImage`。

### 3. 設定選項 (Options)
透過 `ImagePlaygroundOptions` 控制生成細節：
*   **尺寸與比例**：系統會自動根據 `CGSize` 參數映射至最接近的支援解析度。
*   **風格限制**：`ImagePlaygroundGenerationStyle` 可指定預設風格，或透過 `allowed` 清單限制使用者可選的風格。
*   **第三方整合**：加入 `ExternalProvider` 即可啟用使用者在系統層級配置的第三方模型（如 GPT）。

### 4. 特殊格式：Genmoji
當選擇 `ImagePlaygroundStyle.emoji` 時，回傳的結果是 `NSAdaptiveImageGlyph`，這是一種特殊的物件，可直接嵌入 `Text` 或 `TextField` 中，達成類似 Emoji 的行內渲染效果。

## 開發者實用重點

1.  **檢查裝置支援度**：使用 `SupportsImageGeneration` 環境變數來決定 App 是否顯示 AI 生成功能。如果條件不符（如裝置不支援、區域未開通），建議提供 Fallback 選單（例如標準的照片選取器）。
    ```swift
    @Environment(\.supportsImageGeneration) var isSupported
    ```

2.  **檔案管理**：回傳的 `URL` 指向 App 容器內的臨時位置，務必在 Session 結束前將檔案移動或存儲至長效位置。

3.  **無須處理額度 UI**：Apple 會自動管理用戶的使用配額（iCloud Plus 相關），開發者不需要自行設計關於「剩餘次數」或「付費升級」的 UI，系統會自動在適當時機處理。

4.  **個性化設定**：預設啟用「個人化」（允許模型讀取照片庫中的人物特徵），若 App 場景不適用（如純產品圖生成），務必透過 `Options.personalization = .disabled` 將其關閉。

---

**總結：** `ImagePlayground` 是一個「開箱即用」的解決方案。Apple 負責處理複雜的 AI 模型邏輯與隱私保護，開發者只需專注於如何透過 App 內的資料（Context），為用戶創造更具連結感與個人化的視覺體驗。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/375/4/01104285-3253-4b2d-80c3-0d5cdf95c97e/downloads/wwdc2026-375_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/375/
