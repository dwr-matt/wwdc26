# Build real-time neural rendering pipelines with Metal

這是一份關於 WWDC 2026 Session **「Build real-time neural rendering pipelines with Metal」** 的技術摘要。

---

# 技術摘要：使用 Metal 4 構建即時神經渲染管線

本場次由 Apple GPU 軟體工程師 Yulia 主講，重點介紹了如何將機器學習（ML）整合至即時渲染管線中，並利用 Metal 4 的強大效能來提升渲染品質、效能與記憶體效率。演講涵蓋了從「現成方案」到「自定義神經網路」的三個不同層級，為開發者提供了靈活的整合路徑。

---

## 1. 三大層級的機器學習架構

Metal 4 針對不同場景提供了三個層級的 ML 工具：

*   **Metal FX (黑盒解決方案)：** 提供開箱即用的神經降噪（Neural Denoising）與升頻（Upscaling）API。這是針對 Apple Silicon 優化的整合方案，適合追求開發效率與高品質的生產級應用（如 Maxon Redshift）。
*   **Metal 4 ML 命令編碼器 (Command Encoder)：** 允許開發者在 Command Buffer 中直接運行預先訓練好的模型。這提供了更好的控制權與排程調度能力，適用於替換傳統的多階段後處理管線。
*   **Tensor Ops API (高度靈活)：** 提供設計與運行自定義網路的基礎組件，能直接在 Shader 中呼叫。這能完整發揮 M5 與 A19 Pro 晶片中的「神經加速器（Neural Accelerator）」效能，甚至支援線上訓練（Online Training）。

---

## 2. Metal FX 降噪技術與實作最佳實務

Metal FX 是即時光線追蹤渲染中維持高互動性的關鍵。為確保產出品質，開發者應遵循以下最佳實務：

### A. 輸入品質決定輸出結果
*   **保持輸入潔淨：** 降噪器的輸出品質高度依賴 auxiliary inputs（如 albedo, depth, normal）。Diffuse Albedo 是最重要的訊號，應盡量保持其接近無噪點的狀態。
*   **偵錯工具：** 在引擎中建立 Debug Views 來檢查每一層輸入；使用 GPU Capture 逐幀審查紋理是否符合模型預期。

### B. 特殊場景優化
*   **透明度遮罩 (Transparency Overlay)：** 對於霧氣、粒子或天空等無表面幾何資訊、本身已接近無噪的內容，可透過 `transparency overlay` 處理，避免過度降噪。
*   **降噪強度遮罩 (Denoiser Strength Mask)：** 在特定區域（如天空）調低降噪強度（0 到 1 之間），以保護原始質感。

### C. 反射與折射的處理
*   **主表面替換 (Primary Surface Replacement)：** 對於鏡面與玻璃，應結合 Fresnel 係數進行加權，將反射與折射的 albedo 混合，確保反射效果在降噪後仍保持清晰。

### D. 運動向量 (Motion Vectors) 的正確性
*   Metal FX 需要**去抖動（de-jittered）後的運動向量**。
*   **計算邏輯：** 必須從投影座標中減去抖動（jitter）分量。針對動態物體或變形幾何，需儲存頂點在前一幀的世界位置，以計算出正確的 displacement。

---

## 3. 進階應用：自定義神經網路與 Tensor Ops

除了降噪，開發者可以利用機器學習優化後處理管線或光照計算。

### 網路部署流程
1.  **訓練：** 使用 PyTorch 等框架訓練模型，並匯出為 `MTL` 套件格式。
2.  **配置：** 在 Metal 中載入 `MTL` package，並使用 `MTLMachineLearningPipelineDescriptor` 設定網路執行環境。
3.  **執行：** 透過 ML Encoder 進行推論，將多階段後處理（如色調映射、色彩校正）合併為單次神經網路推論。

### Shader 內的小型網路 (Tensor Ops)
對於如「天空光照探針（Sky Probe）」等任務，可訓練微型網路（參數小於數千個），甚至在渲染迴圈中執行**線上訓練（Online Training）**：
*   **線上訓練流程：** 推論（Inference）→ 計算分析解（Analytical Solution）→ 計算誤差（Error）→ 反向傳播（Backpropagation）。
*   **執行策略：** 使用 `CMDigGroup` 執行域，允許多個執行緒協作處理矩陣運算，並利用「協作張量（Cooperative Tensors）」儲存中間結果於 Fast Thread Storage，避免存取主記憶體的效能損耗。

---

## 4. 開發者實用建議

*   **評估需求：** 若只需解決光追渲染雜訊，請直接採用 **Metal FX**；若想優化複雜的後處理管線，請使用 **MTL Package + ML Command Encoder**；若需要即時動態模型（如即時調整光照探針），請嘗試 **Tensor Ops API**。
*   **技術探索：** 下載 Xcode 中的 Metal 4 範例程式碼，並閱讀《Metal Performance Primitives Programming Guide》以了解矩陣運算細節。
*   **思考方向：** 尋找渲染管線中那些傳統上依賴解析方法（Analytical Methods）且運算成本高昂的環節，這些都是遷移至神經網路的絕佳目標。

<!-- resources -->

---

## 資源連結

- [Training a neural network to render irradiance in real time](https://developer.apple.com/documentation/Metal/training-a-neural-network-to-render-irradiance-in-real-time)
- [Metal sample code library](https://developer.apple.com/documentation/Metal/metal-sample-code-library)
- [Download the Metal Performance Primitives (MPP) Programming Guide](https://developer.apple.com/download/files/Metal-Performance-Primitives-Programming-Guide.pdf)
- [Understanding the Metal 4 core API](https://developer.apple.com/documentation/Metal/understanding-the-metal-4-core-api)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/359/5/9da4a720-0dcb-4b8e-b61b-ba8310a61f29/downloads/wwdc2026-359_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/359/5/9da4a720-0dcb-4b8e-b61b-ba8310a61f29/downloads/wwdc2026-359_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/359/
