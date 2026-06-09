# Discover generated subtitles and subtitle styles

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 關於 **Apple AI 生成字幕 (Generated Subtitles)** 與 **字幕樣式預覽 (Subtitle Style Preview)** 的核心開發重點。

---

# WWDC 2026 技術摘要：探索生成的字幕與字幕樣式

## 概述
本場次介紹了兩項提升媒體應用程式無障礙體驗（Accessibility）的關鍵功能：
1. **AI 生成字幕**：透過設備端（On-device）處理，為缺少特定語言字幕的影片自動生成語音轉文字（Speech Transcription）或翻譯字幕（Language Translation）。
2. **字幕樣式預覽**：允許使用者在影片播放過程中，直接在播放器介面內預覽並調整字幕樣式，無需跳轉至系統設定。

這些功能旨在擴大內容受眾，提升使用者在不同環境（如吵雜的機場）或針對不同語言需求下的觀看體驗。

---

## 主要概念與框架

### 1. AI 生成字幕 (Generated Subtitles)
*   **運作機制**：利用設備端的 AI 模型進行處理，無需開發者編寫額外程式碼即可自動生效。
*   **應用場景**：支援 HLS（HTTP Live Streaming）、點播影片（VOD）及檔案串流。適用於專業內容及使用者自製影片。
*   **優選原則**：系統始終優先顯示「製作方提供的字幕 (Authored Subtitles)」，僅在必要時提供生成的備選方案。
*   **UI 標識**：生成的字幕在選單中會標註「閃耀圖示 (Sparkle symbol)」與「Translated/Generated」文字。

### 2. API 與 UI 組件
開發者可透過以下框架實作字幕控制與預覽：
*   **`AVPlayerViewController` (iOS/tvOS/visionOS) 與 `AVPlayerView` (macOS)**：提供「開箱即用」的解決方案，已內建字幕選單、控制項及樣式預覽功能。
*   **`AVLegibleMediaOptionsMenuController`**：當開發者已有自定義播放器介面，但想快速加入字幕選擇與樣式預覽 UI 時，這是最佳組件。
*   **`AVPlayerLayer`**：提供底層 API，用於手動實作樣式預覽邏輯。
*   **`AVCaptionRenderer`**：提供更高級的自定義渲染彈性（開發者需自行負責繪製）。

---

## 技術實作：`AVPlayerLayer` 樣式預覽模式

若要實作自定義的字幕樣式預覽，可依照以下流程使用 `AVPlayerLayer`：

1. **獲取樣式 ID**：從系統中取得各個字幕樣式的 `profile ID`。
2. **觸發預覽**：當使用者選取樣式時，呼叫對應 API：
   ```swift
   // 顯示預覽（範例邏輯）
   playerLayer.showStylePreview(
       style: selectedStyle,
       text: nil, // 傳入 nil 時會顯示本地化的系統預覽文字
       position: offset // 使用 offset 避免遮擋現有 UI
   )
   ```
3. **處理行為**：
   *   當樣式預覽開啟時，現有字幕會自動隱藏，避免衝突。
   *   可多次呼叫以更新預覽樣式。
4. **結束預覽**：當使用者完成選擇後，恢復正常的字幕播放狀態並應用所選樣式。

---

## 開發者實用重點

### 應檢查的事項：
*   **UI 整合**：如果你的應用程式有自定義的播放器控制列，請務必整合字幕選擇功能。不要讓使用者必須離開播放介面才能切換語言。
*   **無障礙優先**：字幕不僅是為了聽障者設計，對所有身處吵雜環境的使用者皆有幫助。支援生成的字幕能顯著提升內容的觸及率。
*   **自動化優勢**：由於生成的字幕是系統自動處理，開發者無需擔憂後端架構或儲存成本，只需確保播放器 UI 能正確顯示系統回傳的字幕選單。
*   **體驗一致性**：建議優先使用 `AVPlayerViewController` 或 `AVLegibleMediaOptionsMenuController` 以確保與系統行為的一致性，僅在 UI 高度客製化需求時才考慮 `AVPlayerLayer` 手動實作。

### 支援度注意：
*   自 iOS/macOS 27 起，支援英語音訊生成英語字幕。
*   在 iOS/macOS 上，支援從現有的英語字幕生成多種其他語言字幕。
*   開發時請參考最新的裝置與語言支援矩陣，以確認目標市場是否在範圍內。

透過這些 API，開發者能輕鬆將專業級的字幕體驗帶入應用程式，打造更具包容性的影音環境。

<!-- resources -->

---

## 資源連結

- [What's new in HTTP Live Streaming](https://developer.apple.com/streaming/Whats-new-HLS.pdf)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/256/4/d28efb5e-5550-468d-b1d1-caec51ce55e6/downloads/wwdc2026-256_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/256/4/d28efb5e-5550-468d-b1d1-caec51ce55e6/downloads/wwdc2026-256_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/256/
