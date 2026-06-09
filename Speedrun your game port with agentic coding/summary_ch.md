# Speedrun your game port with agentic coding

這是一份關於 WWDC 2026 Session「Speedrun your game port with agentic coding」的詳細技術摘要。

---

# 技術摘要：利用 AI 代理加速遊戲移植 (Speedrun your game port with agentic coding)

## 概述
本 Session 由 Metal Ecosystem 團隊的 David Soror 主講，介紹了 **Game Porting Toolkit 4** 的重大更新。核心重點在於引入了「代理式編碼 (Agentic Coding)」工作流，透過一組預先建構的「專家技能 (Expert Skills)」與「Porting Assistant (移植助手)」，協助開發者大幅縮短將 Windows 遊戲（如 D3D12 引擎）移植至 macOS 的時間。這項技術旨在讓 AI 代理負責繁瑣的轉換工作與平台 API 最佳實作，讓開發者能將心力專注於架構決策與高品質的除錯。

---

## 主要概念、API 與框架介紹

### 1. Porting Assistant (移植助手)
這是整個工作流的核心引擎，負責協調移植流程。它遵循三個階段的結構化方法：
*   **Discovery (探索)：** 分析程式碼庫、擷取評估環境的基準資料，並詢問開發者專案需求。
*   **Planning (規劃)：** 將大型專案拆解為具體目標與里程碑，並自動為每個階段載入所需的專家技能。
*   **Validation (驗證)：** 自動執行多點檢查（Launch, API 使用, 著色器驗證, 視覺正確性對比, 記憶體檢測），並透過代理儲存跨里程碑的學習成果。

### 2. Expert Skills (專家技能)
這些是以插件形式提供的模組化能力，涵蓋了從視窗管理到進階渲染的各種專業知識。關鍵技能包括：
*   **窗口與幀率技能：** 處理視窗生命週期、Metal Display Link 渲染迴圈、交換鏈 (Swap Chain) 映射。
*   **渲染與資源技能：** 管理 GPU 記憶體、紋理、渲染目標，以及針對 Apple Silicon Tile 架構的最佳化存取模式。
*   **著色器與轉換技能：** 將 HLSL 轉換為 Metal 著色器，處理描述符表 (Descriptor Tables) 與參數映射。
*   **同步技能：** 處理 D3D12 與 Metal 4 的屏障 (Barrier) 模型映射，確保正確的 GPU 指令同步。

### 3. macOS 27 新工具
*   **GPU Capture / GPU Debug：** 允許 AI 代理自主擷取幀資料、檢查資源綁定、常數緩衝區與管線資料流，實現「自主除錯」能力。
*   **Metal HUD 增強：** 提供對 Metal FX 整合的即時除錯視圖，包含抖動 (Jitter) 分佈圖與曝光參數的可視化。

---

## 示範的技術模式

*   **residency set (常駐集) 註冊：** 示範了在 Metal 4 中，必須在資源使用前將其註冊到 residency set，AI 技能會自動識別此步驟，避免 GPU 存取錯誤。
*   **參數映射邏輯：** 透過 `Metal Shader Converter` 的反射機制 (Reflection) 動態查詢著色器參數，取代舊有的硬編碼偏移計算，解決了因參數對齊不一致導致的渲染錯誤。
*   **Metal FX 整合與調優：**
    *   **Temporal Upscaling：** 自動校準運動向量 (Motion Vectors) 與抖動設置，避免重影 (Ghosting)。
    *   **Frame Interpolation：** 建立專屬展示執行緒，確保插入幀與渲染幀的時間間隔一致，避免延遲。
    *   **即時重載 (Overrides)：** 利用 HUD 在運行時調整抖動乘數與運動向量比例，以快速修正視覺偽影。

---

## 開發者的實用重點

1.  **結構化工作流：** 不要試圖一次完成所有移植。透過 Porting Assistant 將專案拆解為小里程碑，不僅能確保每步都有驗證，還能讓代理更好地銜接上下文。
2.  **善用專家技能庫：** 這些插件已經處理了諸如 GPU 資源同步、記憶體對齊等最容易引發 Bug 的低階問題。當代理載入這些技能時，開發者應信任其最佳實作。
3.  **自主除錯優先：** 在遇到視覺異常時，先讓代理使用新的 `GPU Debug` 工具進行追蹤。這能有效過濾掉無效的猜測，直接定位至指令碼或描述符偏移錯誤。
4.  **從原型到生產：** 該工具不僅適用於小型引擎。在 Session 最後，主講人展示了將 Metal 4 整合至成熟的 **Godot 引擎** 中，顯示此工具具備擴充至生產級專案的潛力。
5.  **環境設定：** 所有技能與插件皆可從 [Game Porting Toolkit GitHub repository](https://github.com/apple/game-porting-toolkit) 下載並安裝。

---

**總結：** Game Porting Toolkit 4 將移植工作從「手動遷移」升級為「協同編碼」。開發者應將自己定位為**架構師與品質把關者**，將繁雜的 API 映射與平台相容性細節交給 Porting Assistant 與專家技能來執行。

<!-- resources -->

---

## 資源連結

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/357/5/5cfd0ceb-598f-4535-9abc-12e22a778326/downloads/wwdc2026-357_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/357/5/5cfd0ceb-598f-4535-9abc-12e22a778326/downloads/wwdc2026-357_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/357/
