# Find your accessory with Bluetooth Channel Sounding

這份技術摘要旨在協助開發者掌握 WWDC 2026 關於 **Bluetooth Channel Sounding（藍牙通道探測）** 的核心應用與實作細節。

---

# 技術摘要：利用 Bluetooth Channel Sounding 尋找周邊配件

## 1. 概述
隨著藍牙技術演進，Apple 在 iOS 中引入了 **Bluetooth Channel Sounding** 技術。這項技術允許僅具備藍牙晶片的配件，在不依賴超寬頻（UWB）的情況下，實現高精度的距離測量。本 Session 強調透過 Core Bluetooth 或 Nearby Interaction 框架，開發者可以為使用者提供精準的空間感知能力，協助定位或管理智慧型裝置（如廚房測溫探針、智慧標籤等）。

## 2. 核心概念與技術架構

### 工作原理
Channel Sounding 採用「相位測距（Phase-based Ranging）」技術：
*   **Initiator (發起者)**：iPhone。
*   **Reflector (反射者)**：藍牙配件。
*   **程序（Procedure）**：iPhone 發送訊號（Tone）給配件，配件將訊號反射回來。iPhone 透過測量訊號在 2.4 GHz 頻段中不同頻道間的相位變化，計算出精確的距離。

### 選項比較
| 功能需求 | 建議框架 | 優勢 |
| :--- | :--- | :--- |
| **僅需距離** | Core Bluetooth | 實作簡單，適合僅有藍牙晶片的配件。 |
| **距離 + 方向** | Nearby Interaction | 結合相機視覺與藍牙測距，提供空間定位（需相機輔助）。 |

---

## 3. API 實作指南

### A. 僅測距：Core Bluetooth
適合只需要知道「距離多遠」的應用場景。
1.  **檢查支援性**：使用 `CBManager.supportsFeatures(.channelSounding)` 確認裝置支援。
2.  **啟動 Session**：在已連接的 `CBPeripheral` 物件上呼叫 `startChannelSoundingSession()`。
3.  **接收結果**：透過 delegate 方法 `peripheral(_:didUpdateChannelSoundingResult:)` 獲取以公尺（meters）為單位的距離。
4.  **結束 Session**：呼叫 `cancelChannelSoundingSession()`。

### B. 測距與方向：Nearby Interaction (NI)
若需要獲取配件的方向（Direction），需結合相機視覺。
1.  **設定 Configuration**：建立 `NINearbyAccessoryConfiguration`，並傳入 Core Bluetooth 的 `peripheral.identifier`。
2.  **啟用相機輔助**：若需取得方位（Direction），務必啟用相機協助功能。
3.  **運行 Session**：建立 `NISession` 並執行。
4.  **提升精準度**：告知系統配件的運動狀態（`accessory.updateMotionState(.stationary / .moving)`），這能顯著優化方向預測的準確度。

---

## 4. 開發者實用重點與硬體需求

### 硬體要求（配件端）
為了確保與 iOS 的 Channel Sounding 功能相容，您的配件必須滿足：
*   **Bluetooth 版本**：必須支援 Bluetooth 6.3。
*   **必要功能**：支援 Inline PCT（Phase-based Code Transmission）。
*   **模式支援**：必須支援 Bluetooth Spec 中的 **Mode 0** 與 **Mode 2**。
*   **時序需求**：支援 TFCS（Interspace timing）至少 **100 微秒**。

### 軟體整合注意事項
*   **權限與狀態**：所有配件必須先透過 **Accessory Setup Kit** 完成配對與設定，並透過 Core Bluetooth 保持連線。
*   **生命週期**：Channel Sounding 僅在 App 處於**前景（Foreground）**時運作；當 App 進入背景，Session 將自動暫停。
*   **系統動態調整**：若環境中藍牙或 Wi-Fi 干擾過大，iOS 可能會主動降低測量頻率，開發者應處理結果可能為 `nil` 的情況。
*   **使用者體驗**：系統會自動對測量結果進行濾波與平滑處理（Smoothing），以確保 UI 顯示的穩定性。

### 適用設備
*   需使用內建 **N1 晶片** 的 iPhone 型號。

---

## 下一步行動
1.  **驗證硬體**：檢查您的藍牙 SoC 是否支援 Bluetooth 6.3 及上述要求的 Mode 0/2。
2.  **API 測試**：利用 Xcode 中的範例專案測試 API，觀察距離讀數與實際物理距離的關聯。
3.  **回饋意見**：若在實作中遇到精準度異常或斷線問題，請透過 Apple Feedback Assistant 提供診斷數據，並參與開發者論壇討論。

<!-- resources -->

---

## 資源連結

- [AccessorySetupKit](https://developer.apple.com/documentation/AccessorySetupKit)
- [Nearby Interaction](https://developer.apple.com/documentation/NearbyInteraction)
- [Core Bluetooth](https://developer.apple.com/documentation/CoreBluetooth)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/369/4/fea90204-fd38-4da4-b9e7-5dce37bc87d8/downloads/wwdc2026-369_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/369/4/fea90204-fd38-4da4-b9e7-5dce37bc87d8/downloads/wwdc2026-369_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/369/
