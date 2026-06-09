# Build AI-powered scripts with the fm CLI and Python SDK

這是一份關於 WWDC 2026 Session「Build AI-powered scripts with the fm CLI and Python SDK」的技術摘要。

---

# WWDC 2026 技術摘要：使用 `fm` CLI 與 Python SDK 建構 AI 驅動腳本

## 概述
本 Session 介紹了 Apple 在 macOS 27 與 iOS 27 中對 **Foundation Models Framework** 的重要更新。除了原有的 Swift API 外，Apple 新增了 `fm` 命令列工具（CLI）以及 Python SDK，讓開發者能更靈活地在終端機或資料科學工作流中，調用 Apple 的裝置端（On-device）模型與私有雲端模型（Private Cloud Compute）。這些工具無需 API Key 且無雲端調用成本，大幅簡化了原型設計與自動化開發流程。

---

## 主要概念與框架介紹

### 1. `fm` 命令列工具 (CLI)
`fm` 是 macOS 27 預裝的工具，旨在讓開發者無需開啟 Xcode 即可測試模型。
*   **常用指令**：
    *   `fm chat`：啟動互動式終端機對話介面。
    *   `fm respond`：用於腳本的單次查詢（適合自動化任務）。
    *   `fm schema`：建立與定義模型結構化輸出的 Schema。
*   **特性**：支援互動式指令（如 `/model` 切換雲端模型、`/save` 儲存對話記錄），並支援傳遞圖片作為 Prompt 的一部分。

### 2. Foundation Models SDK for Python
專為機器學習工程師與資料科學家設計，讓開發者能使用熟悉的 Python 生態系（如 Pandas、Matplotlib）來調用 Apple 模型。
*   **安裝條件**：macOS 27、Apple Silicon Mac、Python 3.10+、已安裝 Xcode。
*   **優勢**：無縫串接開源資料科學套件，適合構建自動化評估管線（Evaluation Pipelines）。

---

## 程式碼模式與技術示範

### A. 使用 `fm` CLI 進行自動化腳本開發
開發者可透過 `fm` 處理非結構化檔案名稱的分類任務。
1.  **定義 Schema**：先透過 `fmSchemaObject` 定義輸出格式（例如：`final_files` 與 `draft_files` 清單）。
2.  **執行回應**：在腳本中呼叫 `fm respond --schema ...`。
3.  **解析與操作**：將模型輸出的 JSON 透過 Bash 或 Python 腳本解析，執行檔案的複製與備份邏輯。

### B. Python SDK：構建評估管線 (Evaluation Pipeline)
Python SDK 讓原型設計與評估變得更高效，範例如下：
*   **定義輸出結構**：使用 `@fm.generable` 裝飾器來確保模型輸出符合定義的類別結構。
*   **模型互動**：
    ```python
    import fm
    session = fm.LanguageModelSession()
    # 使用工具調用 (Tool Calling) 或結構化輸出
    response = session.respond(prompt="...", generating=items_suggestion_schema)
    ```
*   **效能評估 (Evaluation)**：
    *   將多種 Prompt 設定的輸出結果存入 **Pandas DataFrame**。
    *   使用外部「裁判模型」（Judge Model）進行評分。
    *   利用 **Matplotlib** 繪製錯誤率、內容準確度等指標圖表，直觀對比不同 Prompt 的效能優劣。

---

## 開發者的實用重點

1.  **縮短迭代週期**：利用 Python Notebook（如 Jupyter）即時測試 Prompt 並產生評估圖表，省去修改程式碼後重新編譯 Xcode 專案的繁瑣過程。
2.  **善用結構化輸出**：無論是 CLI 的 Schema 還是 Python 的 Generable 裝飾器，利用結構化輸出是確保 AI 應用具備生產力與可靠性的關鍵。
3.  **混合式運算架構**：
    *   **裝置端模型**：適合日常快速測試、隱私優先的任務。
    *   **私有雲端模型**：適合需要更強推理能力、處理複雜問題的場景，且同樣透過 `fm` 工具鏈進行調用。
4.  **從 Swift 到 Python**：如果你已經熟悉 Swift 的 Foundation Models Framework，會發現 Python SDK 的抽象概念幾乎一致，遷移成本極低。

### 建議的下一步：
*   **探索 CLI**：先在終端機嘗試 `fm` 指令，熟悉其互動與結構化輸出功能。
*   **參考 GitHub 資源**：查看 Apple 提供的 Python SDK 官方文件與範例程式碼。
*   **建立評估基準**：挑選一個簡單的專案（如自動化整理、郵件分類），構建一套簡單的評估管線，量化你 Prompt 的改善效果。

<!-- resources -->

---

## 資源連結

- [Foundation Models SDK for Python on GitHub](https://github.com/apple/python-apple-fm-sdk)
- [Foundation Models SDK for Python Documentation on GitHub](https://apple.github.io/python-apple-fm-sdk/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/334/4/65b71eea-f323-4f86-9096-889b6da91bdd/downloads/wwdc2026-334_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/334/4/65b71eea-f323-4f86-9096-889b6da91bdd/downloads/wwdc2026-334_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/334/
