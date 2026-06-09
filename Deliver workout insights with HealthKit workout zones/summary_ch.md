# Deliver workout insights with HealthKit workout zones

這是一份關於 WWDC 2026 Session「**Deliver workout insights with HealthKit workout zones**」的技術摘要，旨在協助開發者快速掌握將心率與功率區間整合至健身 App 的關鍵技術。

---

# 技術摘要：利用 HealthKit Workout Zones 提升健身洞察

## 1. 概述
在 iOS 27 與 watchOS 27 中，HealthKit 新增了對「運動區間（Workout Zones）」的原生支援，涵蓋**心率區間（Heart Rate Zones）**與**自行車功率區間（Cycling Power Zones）**。此功能允許開發者輕鬆存取用戶的強度數據，無論是進行後的訓練總結、即時的教練提醒，還是長期的訓練負載分析，都能透過統一、安全的 API 來達成，幫助用戶更聰明地進行訓練。

---

## 2. 主要概念與框架

### 核心物件與結構
*   **`HKWorkoutZoneGroup`**: 核心數據結構，包含該次訓練的區間設定（`configuration`）以及各區間的持續時間（`zoneDurations`）。
*   **`HKWorkoutZoneConfiguration`**: 描述區間的定義方式，包含：
    *   **Source**: 指出區間來源（自動計算、使用者手動設定、或 App 自定義）。
    *   **Zones**: 一組定義邊界的陣列，保證區間連續且不重疊。
*   **自動化計算**: HealthKit 會根據傳入的原始樣本（Samples）自動計算每個區間的停留時間，開發者無需手動處理累加邏輯。

### 區間來源類型
1.  **系統偏好（Preferred Zones）**: 讀取用戶在「健康」設定中的配置（基於年齡、靜止心率等自動計算，或手動調整）。這是推薦的預設方式，可確保跨 App 的一致體驗。
2.  **自定義配置（Custom Zones）**: 當 App 擁有專有的訓練模型（如特定的功率分區）時使用。此類設定僅在該次 Workout 期間有效。

---

## 3. 技術實作模式

### 讀取歷史區間數據
若要在運動結束後展示總結圖表，可透過 `HKWorkout` 或 `HKWorkoutActivity` 存取數據：
```swift
// 取得心率區間數據
if let zoneGroup = workout.workoutZoneGroup(for: .heartRate) {
    for duration in zoneGroup.zoneDurations {
        print("Zone \(duration.zone.index): \(duration.duration) 秒")
    }
}
```

### 處理即時運動區間更新
透過實作 `HKLiveWorkoutBuilderDelegate` 協議，開發者可以接收即時區間變更通知：
```swift
func workoutBuilder(_ workoutBuilder: HKLiveWorkoutBuilder, didUpdateWorkoutZone zoneUpdate: HKWorkoutZoneUpdate) {
    // 當心率跨越區間時觸發
    let currentZone = zoneUpdate.currentZone
    let previousZone = zoneUpdate.previousZone
    
    // 更新 UI 或觸發語音/震動提醒
    updateUI(for: currentZone)
}
```

### 設定自定義區間
若需使用自定義區間，必須在 `HKWorkoutBuilder` 開始收集數據前配置：
1.  建立 `[HKQuantityZoneBoundary]` 陣列。
2.  建立 `HKWorkoutZoneConfiguration`。
3.  將設定加入 `HKWorkoutBuilder`。
*注意：區間數需介於 3 到 9 個之間。*

---

## 4. 開發者實用重點

*   **自動化優勢**: 不要嘗試自行計算心率樣本落入哪個區間。HealthKit 已經在底層處理了這部分，並提供經過校驗的 `HKWorkoutZoneGroup`，這能大幅降低開發負擔並提升準確度。
*   **多運動類型支援**: `HKWorkoutActivity` 同樣支援區間數據，這對於鐵人三項等「多運動（Multi-sport）」應用至關重要，能細分不同階段的強度。
*   **數據正規化（Normalization）**: 當 App 需要比較不同 Workout 的訓練成效時，請留意不同 Workout 可能使用不同數量的區間（例如有的 5 個區間，有的 7 個）。開發者應針對這些差異進行正規化處理，而非直接比較 `zone3` 的數值。
*   **授權流程**: 存取區間數據需先獲得 `HKQuantityType` (如 `heartRate` 或 `cyclingPower`) 的讀取權限，流程與一般 HealthKit 數據一致。
*   **最佳體驗**: 建議優先檢查 `HKHealthStore` 中的 `preferred` 配置，若無數據再提供自定義選項，以維持 Apple 生態系的一致性。

---

**總結建議**：利用 iOS 27 新增的 Workout Zones API，開發者可以將精力從「處理原始數據計算」轉移到「創造更佳的訓練洞察體驗」。即時區間變更回呼（`didUpdateWorkoutZone`）是提升健身 App 教練功能的關鍵，建議優先導入。

<!-- resources -->

---

## 資源連結

- [Tracking heart rate zones for workouts](https://developer.apple.com/documentation/HealthKit/tracking-heart-rate-zones-for-workouts)
- [Accessing workout zone data](https://developer.apple.com/documentation/HealthKit/accessing-workout-zone-data)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/207/5/8627c1d4-7a34-46f2-8491-f0d1c138edd1/downloads/wwdc2026-207_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/207/5/8627c1d4-7a34-46f2-8491-f0d1c138edd1/downloads/wwdc2026-207_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/207/
