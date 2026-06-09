# Explore distributed inference and training with MLX

這是一份針對 WWDC 2026 Session「**Explore distributed inference and training with MLX**」的技術摘要。

---

# 技術摘要：利用 MLX 進行分散式推論與訓練

隨著本地端大型語言模型（LLM）變得愈加龐大且複雜，單機的記憶體、運算能力或頻寬往往成為效能瓶頸。本 Session 詳細介紹了如何透過 Apple Silicon 強大的硬體堆疊，將多台 Mac 組建成一個分散式運算叢集，藉此實現更大模型的推論、加速微調（Fine-tuning）過程，並優化運算效能。

---

## 1. 核心技術堆疊
Apple 為分散式運算構建了完整的軟體架構，從底層硬體通訊到上層模型訓練框架：

*   **RDMA over Thunderbolt 5**：macOS 26.2 開始支援遠端直接記憶體存取（RDMA）。它能直接在機器記憶體間傳輸資料，大幅降低 CPU 與作業系統的開銷，提供低延遲與高頻寬。
*   **JAKL (Just Another Collective Library)**：由 Apple 開源的集體通訊函式庫。它封裝了複雜的底層通訊，提供開發者高階的原型（如 `all-reduce`），讓任何分散式任務（不限於 ML）都能輕易進行跨機資料交互。
*   **MLX**：Apple Silicon 專用的機器學習框架，與 JAKL 深度整合，負責資源調度、平行運算及分散式工作流的管理。

---

## 2. 叢集架構與通訊拓撲
為了達到最佳效能，開發者可根據需求選擇不同的節點連線方式：

*   **Mesh (全網狀)**：所有節點彼此直接相連。優點是延遲最低，適用於需要高頻頻率通訊的任務（如張量平行處理）。
*   **Ring (環狀)**：節點依序相連。優點是所需線材與連接埠較少，易於擴展。
*   **自動化選路**：JAKL 會根據訊息大小與通訊運算自動切換最優拓撲，在延遲與頻寬間取得平衡。

---

## 3. 實作模式與技術亮點

### 分散式推論 (Distributed Inference)
針對超大型模型（如 1 Trillion 參數的 Kimi 2.6），MLX 提供了兩種平行策略：
1.  **管線平行 (Pipeline Parallelism)**：依「深度」切割模型層，適合處理記憶體極大的模型，通訊負擔較小。
2.  **張量平行 (Tensor Parallelism)**：依「寬度」切割模型，所有節點同時處理相同 Token，顯著提升推論速度，但對網路延遲要求較高。

### 分散式微調 (Data Parallel Training)
採用**資料平行 (Data Parallelism)** 模式：
*   在每台機器上複製模型實例。
*   每台機器處理不同的資料 Batch，計算梯度後透過 `all-reduce` 同步梯度平均。
*   實現近乎線性的效能提升（$N$ 台機器的速度約為單機的 $N$ 倍）。

### 程式碼與工具使用
*   **`mlx-distributed config`**：自動掃描 Thunderbolt 連線並產生 JSON 設定檔的輔助工具。
*   **`mlx launch`**：CLI 啟動器，負責透過 SSH 初始化叢集並同步執行指令。
*   **API 支援**：除了 CLI，開發者可透過 **Python、Swift 與 C++** API 進行細粒度的 sharding 設定（如 `shard_linear` 函式）與通訊控制。

---

## 4. 開發者實用重點 (Key Takeaways)

1.  **最低改動門檻**：開發者只需使用 `mlx launch` 封裝既有的單機訓練/推論指令，即可將工作遷移至叢集，程式碼邏輯無需大幅重寫。
2.  **硬體要求**：務必確保叢集內的機器均啟用 `Enable RDMA over Thunderbolt` 選項。
3.  **環境設定關鍵**：執行分散式任務時，務必設定環境變數 `MLX_METAL_FAST_SYNC=1`，這能優化 GPU 與 CPU 間的同步，對降低分散式通訊成本至關重要。
4.  **隱私性**：此架構完全在本地端叢集運行，訓練資料完全不會離開使用者的機器，符合對隱私敏感的應用場景。
5.  **擴展性**：不僅限於訓練，MLX LM 內建的伺服器模式也支援分散式部署，適合開發本地端高吞吐量的 Agent 應用。

---

*想進一步深入研究？建議參閱官方文件關於自訂平行策略 (Custom Parallelism Strategies) 與訓練循環 (Training Loops) 的詳細說明。*

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
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/233/4/379c319a-5718-4fd2-aac6-2f97180c5892/downloads/wwdc2026-233_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/233/4/379c319a-5718-4fd2-aac6-2f97180c5892/downloads/wwdc2026-233_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/233/
