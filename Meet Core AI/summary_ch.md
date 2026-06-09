# Meet Core AI

這份技術摘要旨在協助開發者快速掌握 WWDC 2026 **「Meet Core AI」** Session 的重點。Core AI 是 Apple 為在裝置端（On-device）執行高效能 AI 推理所推出的全新框架，旨在銜接模型開發與應用程式整合的生命週期。

---

### 1. 概述：Core AI 是什麼？
Core AI 是 Apple Intelligence 的底層推理引擎，現已開放給全體開發者使用。它不僅是一個框架，更是一整套針對 Apple Silicon（CPU/GPU/Neural Engine）優化的技術堆疊，涵蓋了從模型優化、轉換、除錯到整合的完整生命週期。Core AI 的核心目標是讓開發者能夠在無伺服器、零 Token 成本的情況下，於 Apple 裝置本地運行從小型的專用模型到大型語言模型（LLM）。

### 2. 主要概念與技術架構

*   **模型轉換流程**：使用 `coreai-torch` Python 套件，將 PyTorch 模型匯出並轉換為 Core AI 格式。過程中可利用動態形狀（Dynamic Shapes）與分解表（Decomposition Table）來確保模型在不同輸入長度下的相容性與效能。
*   **Swift 推理框架**：提供現代化的 Swift API，利用 Swift 的「非逃逸型別」（Nonescapable types）來確保記憶體安全（Memory Safety）的同時，維持極高的運算效能。
*   **核心組件**：
    *   `AIModel`：模型實例，用於檢查中繼資料與載入推理函式。
    *   `InferenceFunction`：可執行的計算圖。
    *   `NDArray`：處理多維張量輸入/輸出的核心資料結構。
    *   `States`：用於處理循環推理（如 Transformer 的 KV Cache），減少重複計算並降低延遲。
*   **模型特化（Specialization）與快取**：模型在執行前會經歷「特化」過程（針對特定裝置編譯），此步驟較為耗時。開發者可透過 `AIModelCache` 進行手動管理或預先編譯，避免在使用者互動流中發生卡頓。

### 3. 實作模式與關鍵技術

在 Session 的「貪食蛇遊戲 AI」範例中，展示了以下關鍵開發模式：

*   **KV Cache 優化（States）**：
    透過 `torch.register_buffer` 在 PyTorch 中註冊狀態，Core AI 會將其轉換為可變的 `State` 引數。在推理循環中，將 KV Cache 作為狀態傳入，實現「原地更新」，從而解決因序列長度增加導致的推理效能下降問題。
*   **非同步與記憶體配置**：
    為了在緊湊的推理迴圈中消除開銷，開發者可以：
    *   根據最佳記憶體佈局分配 `NDArray`。
    *   預先配置輸出緩衝區以避免記憶體重新分配。
    *   使用非同步 API 實現多個推理功能的管線化執行（Pipelining）。
*   **數值除錯**：
    使用 Core AI Debugger 在 Xcode 中進行視覺化，直接將轉換後的張量數值追溯至原始 Python 程式碼，確保數值準確度與 PyTorch 原始版本一致。

### 4. 給開發者的實用重點（Tips）

1.  **先行規劃模型載入**：模型第一次執行時需進行「特化」，建議在 App 初始化或背景下載完成後執行，不要放在使用者點擊按鈕後的互動路徑中。
2.  **善用工具鏈**：
    *   **Instruments**：監測推理間隔（Inference Intervals），及早發現因模型複雜度導致的效能瓶頸。
    *   **Debug Gauge**：即時觀察 Core AI 的活動狀況。
3.  **Ahead-of-Time (AOT) 編譯**：針對大型模型，建議在開發階段進行 AOT 編譯，大幅縮短使用者裝置上的首次執行延遲。
4.  **資源連結**：
    *   **Core AI Models Repository**：官方提供了一系列可直接轉換與優化的模型庫。
    *   **進階開發參考**：若需自定義 GPU Kernel 或深度自定義模型，請查閱相關的進階 Authoring 與 Optimization Session。

---
**總結**：Core AI 透過整合 Swift 的安全特性與 Apple Silicon 的硬體效能，簡化了複雜的 AI 整合工作。對於想要在 App 中引入 AI 功能的開發者，現在可以透過熟悉的 PyTorch 工作流程，無縫地將模型遷移至高效能的 Apple 裝置生態系統中。

<!-- resources -->

---

## 資源連結

- [Core AI PyTorch Extensions](https://apple.github.io/coreai-torch)
- [Core AI Python](https://apple.github.io/coreai-torch/main/coreai-core)
- [Core AI Optimization](https://apple.github.io/coreai-optimization)
- [Core AI](https://developer.apple.com/documentation/CoreAI)
- [Compiling Core AI models ahead of time](https://developer.apple.com/documentation/CoreAI/compiling-core-ai-models-ahead-of-time)
- [Managing model specialization and caching](https://developer.apple.com/documentation/CoreAI/managing-model-specialization-and-caching)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/324/4/3b67b624-4060-495f-9ba7-659805ee6b88/downloads/wwdc2026-324_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/324/4/3b67b624-4060-495f-9ba7-659805ee6b88/downloads/wwdc2026-324_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/324/
