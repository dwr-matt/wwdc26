# Run local agentic AI on the Mac using MLX

這是一份針對 WWDC 2026 Session「Run local agentic AI on the Mac using MLX」的技術摘要。

---

# 技術摘要：使用 MLX 在 Mac 上執行本機 Agentic AI

本 Session 展示了如何利用 Apple 的 **MLX 框架**，在不依賴雲端 API 的情況下，於本機 Mac 上建構並執行具備「代理能力」（Agentic）的 AI 工作流程。透過將數據留在本機，開發者不僅能提升隱私安全性，還能降低開發成本，並透過 Apple Silicon 的硬體加速實現極高的執行效率。

---

## 1. 核心架構：本地 Agentic AI 技術堆疊

講師將本地 AI 代理的運行環境歸納為四個層次，由底層到高層分別為：

1.  **MLX (底層運算框架)**：Apple 開源的陣列運算框架，專為 Apple Silicon 設計，負責 Metal 加速、記憶體管理與低階計算。
2.  **MLX LM (語言模型層)**：提供載入、運行、量化與微調 LLM 的工具（Python API 及 CLI），支援 Hugging Face 上數千種模型。
3.  **MLX LM Server (服務層)**：一個與 OpenAI API 相容的 HTTP 伺服器，支援「結構化工具調用」（Structured Tool Calling）與推理模型（Reasoning Models），可作為雲端 LLM API 的直接替代品。
4.  **Agent (應用層)**：任何支援 OpenAI Chat Completions 協議的開發工具或框架（如 Xcode、OpenCode、PyAgent 等）。由於 MLX LM Server 提供標準介面，現有代理框架幾乎可直接對接。

---

## 2. 關鍵技術優化

為了克服本地運行 Agent 時遇到的效能瓶頸，MLX 導入了三項關鍵優化：

*   **神經網路加速器 (Neural Accelerators)**：M5 晶片導入了專用加速單元。MLX 自動針對這些單元進行矩陣運算優化，使 M5 上的矩陣乘法速度較 M4 快 4 倍。這大幅縮短了處理 Agent 循環中「提示詞處理」（Prompt Processing）的時間。
*   **連續批次處理 (Continuous Batching)**：MLX LM Server 支援動態將多個併發請求（如子代理同時進行程式碼搜尋、測試撰寫）分組並在 GPU 上並行處理，防止請求排隊導致的效能延遲。
*   **分散式推論 (Distributed Inference)**：針對超大型模型（如 1.6 兆參數的 DeepSeek），MLX 支援將模型分佈在多台 Mac 上（透過 Thunderbolt 或乙太網路）。macOS 26.2 引入了 **Thunderbolt RDMA** 技術，能提供極低延遲的節點間通訊，在 4 節點配置下可實現高達 3 倍的效能提升。

---

## 3. 開發者實作流程

要從零開始建立本地 Agent 工作流程，僅需三個步驟：

1.  **安裝**：透過 `pip install mlx-lm` 安裝必要的函式庫。
2.  **啟動伺服器**：執行 `mlx-lm-server` 並指定一個支援工具調用的模型。
    ```bash
    # 範例啟動指令
    mlx-lm-server --model <model-name> --port 8080
    ```
3.  **串接代理**：在 Agent 框架的設定檔中，將 API Base URL 指向 `http://localhost:8080`。例如在 OpenCode 等工具中設定 Provider URL 為 localhost，Agent 即會透過本地端進行所有推理與操作。

---

## 4. 開發者實用重點總結

*   **隱私保護**：所有數據（包含原始碼、檔案內容）均在 Mac 內部處理，不會傳送至雲端。
*   **無縫整合 Xcode**：現在 Xcode 可透過「Intelligence」設定面板，直接連接本地運行的 MLX Server，讓 Xcode 原生具備讀取專案結構、分析編譯錯誤、自動修復 Bug 的能力。
*   **零侵入性優化**：MLX 自動偵測硬體並選擇最佳運算核心（Kernel），開發者無需為特定硬體編寫額外程式碼即可獲得加速。
*   **生態系支援**：如 Ollama、LM Studio 等工具早已整合 MLX，開發者可直接利用這些工具的基礎設施，無需從頭建構。
*   **迭代開發**：本地 Agent 不僅能撰寫新專案，還能透過「自動重試與編譯」（Build and Fix loop）來處理開發過程中的錯誤，適合快速迭代 SwiftUI 等現代應用開發。

---
*註：相關開發資源、範例程式碼與 MLX 文件皆已開源，開發者可前往 Apple 開發者官網或 MLX GitHub 專案進行進一步探索。*

<!-- resources -->

---

## 資源連結

- [MLX Swift LM on GitHub](https://github.com/ml-explore/mlx-swift-lm)
- [MLX Swift Examples](https://github.com/ml-explore/mlx-swift-examples)
- [MLX Examples](https://github.com/ml-explore/mlx-examples)
- [MLX Swift](https://github.com/ml-explore/mlx-swift)
- [MLX  LM - Python API](https://github.com/ml-explore/mlx-lm)
- [MLX Explore - Python API](https://github.com/ml-explore/mlx)
- [MLX Framework](https://mlx-framework.org)
- [MLX](https://ml-explore.github.io/mlx/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/232/4/f309be4a-8e5b-4c0f-843a-fcbd84c5e2d1/downloads/wwdc2026-232_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/232/4/f309be4a-8e5b-4c0f-843a-fcbd84c5e2d1/downloads/wwdc2026-232_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/232/
