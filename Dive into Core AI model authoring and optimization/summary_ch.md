# Dive into Core AI model authoring and optimization

這份技術摘要旨在協助開發者深入了解 **CoreAI** 框架，這是一套專為 Apple Silicon 裝置設計的全方位模型部署與優化解決方案。

---

# WWDC 2026 技術摘要：Dive into Core AI model authoring and optimization

## 概述
本 Session 深入探討了 CoreAI 生態系統，旨在幫助開發者將 PyTorch 模型高效地轉換、優化並部署至 Apple Silicon（macOS/iOS）。講者介紹了從模型開發、轉換（Conversion）、壓縮（Optimization）到除錯（Debugging）的完整生命週期，並特別強調了透過「模型重新架構（Model Re-authoring）」來提升效能的進階技巧。

---

## 主要概念與框架元件

### 1. CoreAI Models Repository 與 Skills
*   **Models Repository**：一個開源模型庫，包含大型語言模型（LLM）等生成式架構，提供標準化的 Swift 套件與模型組件，讓開發者快速上手。
*   **CoreAI Skills**：這是一套「專家知識庫」，可整合至現有的 AI 編碼助手（Coding Agent）中，協助開發者規劃部署路徑、根據硬體限制選擇優化方案，並撰寫符合最佳實踐的 PyTorch 程式碼。

### 2. CoreAI Python Ecosystem
*   **CoreAI Torch**：核心轉換庫。透過 `pip install coreai-torch` 安裝，能將 PyTorch 的 `ExportedProgram` 直接轉換為 CoreAI 格式。
*   **CoreAI Opt**：模型優化庫，支援權重壓縮（int4, int8, fp4, fp8）與多種量化配置（config-driven），適用於不同層級的壓縮需求。
*   **CoreAI Debugger**：全新的視覺化除錯工具，用於分析模型圖結構、對比 PyTorch 原始輸出與轉換後的執行結果，並透過 PSNR 等指標快速定位量化導致的誤差。

---

## 核心技術與程式碼模式

### A. 模型轉換與優化管線
基本的轉換流程如下：
1.  **PyTorch Export**：使用 `torch.export` 捕捉運算圖。
2.  **轉換**：使用 `CoreAI.Torch.converter` 將圖檔轉換。
3.  **優化**：透過 `CoreAI.Opt` 的 `quantizer` 對特定層進行量化。
4.  **執行**：透過提供對應輸入名稱的字典（NumPy tensors）進行推論。

### B. 自定義 Metal Kernels
若開發者有極致效能需求，可直接嵌入 **Metal Shading Language (MSL)** 核心：
*   定義 PyTorch 參考實作（用於 tracing）。
*   編寫對應的 MSL 程式碼。
*   使用 `register_coreai_torch_metal_kernel` 將兩者綁定。
*   該 kernel 會直接嵌入模型資產中，無需額外外部檔案。

### C. 模型重新架構 (Model Re-authoring)
這是本次介紹的最進階技術，適用於 iOS 等受限環境：
*   **模組化拆解**：將單體模型（如 SAM3）拆分為 `ImageEncoder`、`TextEncoder` 與 `Detector` 三個獨立函數。
*   **優勢**：
    *   **分段執行**：允許不同模組以不同節奏執行（例如：提示詞不變時，僅重跑解碼器，效能可提升 76%）。
    *   **針對性壓縮**：對敏感層（如 Detector）保留高精度，對編碼層實施強壓縮。
    *   **專用算子**：在 PyTorch 程式碼中使用卷積投影（Convolutional Projections）取代線性層，以觸發 Apple Silicon 原生硬體加速。

---

## 開發者實用重點 (Key Takeaways)

1.  **視覺化除錯是關鍵**：當模型壓縮後出現準確度下降（如 SAM3 的漏檢問題），不要盲目猜測。使用 **CoreAI Debugger** 的「比較模式」，並透過 `Save Intermediates API` 匯出 PyTorch 與 CoreAI 的中間層輸出，能精準定位問題層。
2.  **優先選擇「不對稱壓縮」**：並非所有層都適合量化。利用 Debugger 發現對準確度影響較大的層，在壓縮配置中將其設為忽略（Exclude），能以極小的模型體積損失換取準確度。
3.  **善用 CoreAI Skills 縮短學習曲線**：講者強調大部分程式碼皆由 AI 助手透過 CoreAI Skills 輔助完成。建議開發者優先查閱 Models Repository 中的範例，直接套用已驗證的最佳實踐。
4.  **動態輸入的處理**：在自定義 Metal 核心時，務必在每個呼叫點明確提供 `result shapes`，這對處理動態輸入大小的模型至關重要。

---
*註：若需深入瞭解 Metal 算子優化細節，建議參考 Session：「Optimize Custom Machine Learning Operations with Metal Tensors」。*

<!-- resources -->

---

## 資源連結

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Inspecting, debugging, and profiling Core AI models](https://developer.apple.com/documentation/CoreAI/inspecting-debugging-and-profiling-core-ai-models)
- [Inspecting Core AI models with Core AI Debugger](https://developer.apple.com/documentation/CoreAI/inspecting-core-ai-models-with-core-ai-debugger)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/325/5/8d08c9d4-3c64-49e1-8590-8b76bd9ad4cb/downloads/wwdc2026-325_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/325/5/8d08c9d4-3c64-49e1-8590-8b76bd9ad4cb/downloads/wwdc2026-325_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/325/
