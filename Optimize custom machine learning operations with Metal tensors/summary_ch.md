# Optimize custom machine learning operations with Metal tensors

這是一份針對 WWDC 2026 Session「Optimize custom machine learning operations with Metal tensors」的技術摘要。

---

# 技術摘要：利用 Metal Tensors 優化自定義機器學習運算

## 概述
本場 Session 深入探討了如何利用 **Tensor Ops** 函式庫編寫高效的 Metal 核心（Kernels），以優化 Apple Silicon 上的機器學習任務。重點涵蓋了量化資料（Quantized Data）的處理、高效能運算核心（如 Flash Attention）的實作，以及如何將這些自定義的核心整合至 CoreAI 工作流程中，達到極致的硬體加速。

---

## 主要概念與技術架構

### 1. Tensor Ops 函式庫
Tensor Ops 是一套 Metal Shading Language (MSL) API，旨在 GPU 上加速矩陣乘法（MatMul）與卷積運算。它會自動適配不同的 Apple Silicon 硬體代數，並針對 **M5 晶片家族的 Neuron Accelerator** 進行硬體級優化。

### 2. 量化與資料儲存
為了解決大規模模型在推理時記憶體頻寬受限（Memory Bandwidth Bound）的問題，Session 強調了量化的重要性：
*   **支援的資料類型**：除了既有的 16-bit 浮點數，Tensor Ops 現在原生支援 4-bit/8-bit 整數，並在 iOS/macOS 27 擴展至 4-bit/8-bit 浮點數與 2-bit 整數。
*   **Scale Factors（縮放因子）**：透過 `MTLBuffer` 中的 `scales plan`，單一 Tensor 物件現在能同時包含「量化資料平面」與「縮放平面」。支援 **FP8 EAM0** 格式，將資料與縮放因子封裝在同一個 Tensor 物件中。

### 3. 進階運算核心：Flash Attention
Flash Attention 將 Q/K/V 矩陣乘法與 Softmax 運算融合，減少記憶體存取次數。透過 Tensor Ops 的 **Cooperative Tensors**，資料可以在 Thread Group 的暫存器（Registers）之間直接傳遞，避免將中間結果寫入記憶體。

---

## 開發者實用實作模式

### 量化 Tensor 的宣告
透過輔助平面映射（Auxiliary Plane Map）將 scales 連接至 Tensor Descriptor：
```cpp
// 建立 Scales 平面描述符
auto scaleDesc = Descriptor(...);
scaleDesc.setDataType(DataType::FP8_EAM0);
scaleDesc.setBlockSize(32, 1); // 每 32 個元素共用一個 Scale

// 連結至主 Tensor
auto tensorDesc = Descriptor(...);
tensorDesc.setAuxiliaryPlaneMap(scaleDesc);
```

### 利用 Cooperative Tensors 優化效率
為了避免「寫入記憶體再讀取」的開銷，開發者可以使用 `Cooperative Tensor` 在暫存器層級進行處理：
1.  **直接傳遞**：呼叫 `getLeftInputCooperativeTensor` 方法。
2.  **相容性檢查**：在重用 Cooperative Tensor 作為輸入前，必須呼叫 `isCompatibleAsLeftInput` 或 `isCompatibleAsRightInput` 進行檢查。
3.  **條件式回退**：若佈局不相容，則需透過 Thread Group Memory 進行中轉。

---

## 開發者重要提示 (Best Practices)

1.  **硬體對齊要求**：新的量化資料類型（如 FP8/2-bit 整數）對記憶體對齊有更嚴格的要求，請務必參閱最新的 Metal 文件。
2.  **Thread Group 平行化**：在編寫矩陣乘法時，應將問題拆分為多個 Thread Groups，並使用 `slice` 操作將輸入資料與對應的 scales 平面同時切分。
3.  **整合至 CoreAI**：
    *   開發者可以使用 Python 定義自定義的 Metal Kernel 字串。
    *   透過 `Torch metal kernel` 物件註冊，即可將其無縫替換掉 HuggingFace 模型中的預設算子。
    *   最終透過模型轉換工具，將其匯出為經過優化的 CoreAI 資源檔（.coreai），應用於如 SAM3 等影像分割模型中。
4.  **調適與除錯**：若無法直接將量化 Tensor 丟入 Tensor Ops，最簡單的除錯路徑是先將區塊（chunk）載入 Thread Group Memory，解量化為 `F16` 後再進行計算，待效能優化需求產生後，再轉為使用 Cooperative Tensors。

---

**建議學習路徑：**
*   參考 Tensor Ops 官方範例程式碼。
*   觀看「Deep Dive into CoreAI Model Authoring」以了解如何將上述 Metal Kernel 實際打包進應用程式中。

<!-- resources -->

---

## 資源連結

- [Running inline ML operations in a shader with Metal 4](https://developer.apple.com/documentation/Metal/running-inline-ml-operations-in-a-shader-with-metal-4)
- [Machine learning passes](https://developer.apple.com/documentation/Metal/machine-learning-passes)
- [Download the Metal Performance Primitives (MPP) Programming Guide](https://developer.apple.com/download/files/Metal-Performance-Primitives-Programming-Guide.pdf)
- [Metal Performance Shaders](https://developer.apple.com/documentation/MetalPerformanceShaders)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/330/4/0ff2c290-e47b-4d88-8a8f-0634e11506a4/downloads/wwdc2026-330_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/330/4/0ff2c290-e47b-4d88-8a8f-0634e11506a4/downloads/wwdc2026-330_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/330/
