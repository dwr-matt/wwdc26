# Explore numerical computing in Swift with MLX

這是一份為開發者整理的 WWDC 2026 Session「Explore numerical computing in Swift with MLX」技術摘要。

---

# WWDC 2026 技術摘要：在 Swift 中探索數值運算與 MLX

## 概述
本 Session 由 David Kosky 主講，介紹了如何利用 **MLX Swift** 在 Apple 平台上進行高效的數值運算（Numerical Computing）。MLX 不僅限於機器學習，它是一個強大的數值計算框架，能夠讓開發者以接近數學表達式的方式編寫程式碼，同時獲得 GPU 加速與自動微分等進階效能特性。

## 主要概念與框架介紹

### 1. 為何選擇 MLX Swift？
在 Apple 的生態系中已有許多數值工具（如 Accelerate, BNNS, Metal Performance Shaders, Swift Numerics），而 MLX Swift 的定位在於：
*   **直觀性**：程式碼結構與數學公式高度一致，減少底層實作的瑣碎細節（bookkeeping）。
*   **效能**：內建自動 GPU 執行與延遲計算（Lazy Evaluation）。
*   **表達力**：以多維陣列（N-dimensional arrays）為核心抽象，API 設計深受 NumPy 使用者喜愛。
*   **開源**：採用 MIT 授權，社群活躍，鼓勵提交 Issues 與 PR。

### 2. 核心運作機制
*   **延遲計算 (Lazy Evaluation)**：MLX 的操作不會立即執行，而是構建一個計算圖（Compute Graph）。唯有在呼叫 `eval()` 或嘗試讀取結果時，才會執行運算。這使得系統能優化執行路徑，並實現自動微分。
*   **自動微分 (Automatic Differentiation)**：透過 `grad` 函式轉換，MLX 可以自動計算複雜函數的梯度，無須手寫導數公式。

## 示範的程式碼模式與技術

講者透過三個範例展示了 MLX 的威力：

### A. 矩陣運算 (Power Iteration)
展示了如何將數學運算直接翻譯為程式碼：
*   **矩陣轉置與加法**：`B.T` 與 `+` 運算符的直覺使用。
*   **計算效率**：在迴圈中使用 `eval()` 來定期清理計算圖，防止圖形過度膨脹。

### B. 分形與濾鏡 (Mandelbrot Set & Convolution)
*   **矩陣化處理**：摒棄傳統逐點（scalar-by-scalar）迴圈，改為對整個陣列進行矩陣乘法與加法，直接由 GPU 平行處理，效能可提升約 10 倍。
*   **卷積運算 (Convolution)**：使用 `conv2d` 處理鄰居關係（如熱分佈模擬）。透過定義卷積核心（Kernel），將複雜的鄰域運算簡化為單次函數呼叫。
*   **進階數值技巧**：展示了「Successor Over-Relaxation (SOR)」演算法，透過棋盤遮罩（Checkerboard Mask）實現原地更新（In-place update）的效果，顯著加速收斂。

### C. 曲線擬合 (Curve Fitting)
*   **梯度下降 (Gradient Descent)**：定義損失函數（如均方誤差），利用 MLX 的 `grad` 自動求導，並透過簡單的迴圈不斷更新參數，實現對數據的擬合。

## 開發者的實用重點

1.  **跨平台優勢**：MLX 支援 Swift, Python, C++ 與 C。開發者可以選擇在 Python 進行快速原型設計（Research），再遷移到 Swift 進行產品部署（Production）。
2.  **生態系資源**：
    *   `MLX Swift`：核心框架。
    *   `MLX Swift LM`：專注於語言模型實作。
    *   `MLX Swift Examples`：包含本文提到的所有範例（如 Mandelbrot、熱分佈、穩定擴散模型與 LLM 微調）。
3.  **入門建議**：
    *   若你熟悉 NumPy，MLX 將讓你感到非常親切。
    *   利用 Swift Package Manager 即可快速整合到專案中。
    *   務必善用 `eval()` 來控制記憶體佔用與效能表現。

**結語**：MLX Swift 是在 Apple 硬體上進行高效數值模擬、視覺化運算或機器學習任務的首選。開發者應善用其矩陣化運算的能力，取代傳統的逐點迴圈，從而大幅提升應用程式的效能與程式碼的可讀性。

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
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/328/5/51d0ab0a-f401-4514-9f04-6b211897d3e8/downloads/wwdc2026-328_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/328/5/51d0ab0a-f401-4514-9f04-6b211897d3e8/downloads/wwdc2026-328_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/328/
