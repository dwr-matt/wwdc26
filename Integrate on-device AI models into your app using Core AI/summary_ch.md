# Integrate on-device AI models into your app using Core AI

這是一份關於 WWDC 2026 Session「**Integrate on-device AI models into your app using Core AI**」的技術摘要，旨在協助開發者掌握在 Apple 裝置上部署本地 AI 的核心流程。

---

# 技術摘要：利用 Core AI 將裝置端 AI 模型整合至應用程式

## 概述
本 session 介紹了 Apple 新推出的 **Core AI** 框架，該框架旨在讓開發者能將先進的 AI 功能直接運行於裝置本地（On-device）。核心願景是讓使用者資料留在裝置內，不僅能保護隱私，還能免除伺服器管理成本、Token 費用及網路延遲。講師 Karina 透過一個「語言學習應用程式」實例，展示如何結合視覺模型（Vision Transformer）與大型語言模型（LLM），實現從圖片自動生成學習卡片的完整工作流。

---

## 主要概念與技術架構

### 1. 雙模型架構（Model Decomposition）
為兼顧效能與記憶體限制，講師建議將複雜任務分解為多個小型、專用模型：
*   **視覺處理**：使用 `SAM3` (Segment Anything Model 3) 處理圖像分割，從相機畫面中提取用戶指定的物體。
*   **邏輯推理**：使用多語言大型語言模型（如 `Quinn` 系列）進行語義解析、翻譯及生成例句。
*   **優勢**：模型更小、品質更高、可獨立更新。

### 2. 模型取得與轉換
開發者有多種途徑將模型導入專案：
*   **Core AI Models Repo**：官方提供的模型目錄，包含熱門模型的轉換腳本與最佳化食譜（Recipes）。
*   **轉換工具**：透過 PyTorch Extensions Package 將 PyTorch 模型轉換為 Core AI 格式。
*   **優化包**：利用 Core AI Optimization Package 進行模型壓縮，以符合行動裝置的記憶體約束。

---

## 程式碼模式與實作技術

### 1. 模型介面與抽象化
Core AI 透過 Swift 套件封裝了繁瑣的張量處理，開發者無需直接處理底層 Tensor Shapes：
*   **Swift API**：提供乾淨的介面執行推論（Inference），自動處理預處理（Preprocessing）與後處理（Post-processing）。
*   **導入流程**：
    ```swift
    // 範例：加載並執行分割模型
    let segmenter = CoreAIImageSegmenter(modelBundle: sam3Bundle)
    let mask = try segmenter.segment(image: inputImage, prompt: "flower")
    ```

### 2. 結構化輸出（Guided Generation）
Core AI 支援「引導式生成」，讓模型輸出符合定義的資料結構，而非隨機文字：
*   使用 `generable` 巨集定義 Vocabulary Card 的欄位結構（如：詞彙、翻譯、例句），確保輸出的資料能直接被 App 邏輯解析。

### 3. 多平台共享邏輯
Core AI 允許開發者在 iOS 與 macOS 間共用相同的 AI 代碼路徑：
*   **彈性部署**：在 macOS 上可選用參數更多（如 8B）的模型版本，以獲得更高的推理品質或處理複雜的批次任務（Batch Processing）。

---

## 開發者實用重點與效能優化

### 1. 模型專門化（Specialization）與快取
模型在首次運行前會進行「專門化」（Specialization）以產生硬體執行檔，此步驟較耗時：
*   **建議策略**：將專門化過程安排在「首次啟動引導頁（First-run experience）」而非互動的核心路徑中。
*   **快取機制**：一旦完成專門化，後續推論將直接使用快取，效能將顯著提升。

### 2. Ahead-of-Time (AOT) 編譯
為進一步減少使用者等待時間，開發者可使用 `coreai-build` 命令列工具：
*   在開發環境提前編譯針對特定架構的執行檔。
*   透過「背景資源（Background Assets）」動態下載已編譯的模型，避免 App 安裝包過於臃腫。

### 3. 部署建議
*   **發現機制**：不要在初始下載包中包含所有模型（以免體積過大），應將模型下載作為 App 內的選項功能，由使用者觸發。
*   **儀表板工具**：使用 Core AI Instruments 進行追蹤，識別模型載入與效能瓶頸（如 Specialization 耗時），並針對性地進行優化。

---

## 結語
Core AI 為開發者開啟了裝置端智慧的新篇章。透過「視覺分割 + LLM 推理」的組合，結合 AOT 編譯與背景資源下載，開發者能在維持高品質體驗的同時，將強大的 AI 能力無縫帶入 Apple 生態系。

**建議資源**：
*   查看 *Meet CoreAI* session 了解設計哲學。
*   參閱 *Dive into CoreAI Model Authoring and Optimization* 學習模型轉換與優化技術。
*   參考 *Compiling CoreAI Models Ahead of Time* 文件獲取編譯優化詳情。

<!-- resources -->

---

## 資源連結

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/326/5/7ff038e2-12cb-4b92-9f49-1d051db7ce5d/downloads/wwdc2026-326_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/326/5/7ff038e2-12cb-4b92-9f49-1d051db7ce5d/downloads/wwdc2026-326_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/326/
