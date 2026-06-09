# Expand the capabilities of your Virtualization app

這是一份針對 WWDC 2026 Session「Expand the capabilities of your Virtualization app」的詳細技術摘要。

---

# 技術摘要：擴展您的虛擬化應用程式功能

本 Session 介紹了 Apple Virtualization 框架在 macOS 26/27 中的重大更新，旨在幫助開發者建構更強大、更靈活的虛擬化解決方案。內容涵蓋從 macOS 虛擬機自動化部署、硬體周邊存取、先進網絡拓撲，到高效磁碟管理及自定義 VIRTIO 裝置等進階開發技術。

---

## 主要概念、API 與框架介紹

### 1. macOS 客戶端自動化部署 (Guest Provisioning)
過去手動設定 macOS 虛擬機（Setup Assistant）過程冗長，現在框架支援編程化設定。
*   **API**: `VZMacGuestProvisioningOptions` 與 `VZMacOSVirtualMachineStartOptions`。
*   **功能**: 可在啟動時自動建立使用者帳號、設定密碼、啟用自動登入（Auto Login）及 SSH 遠端存取。
*   **限制**: 僅在虛擬機尚未完成初始設定前有效。

### 2. Accessory Access 框架
讓使用者能明確控制 USB 裝置的存取權限。
*   **目的**: 解決虛擬機需要存取宿主機 (Host) USB 裝置的需求，同時兼顧安全性與使用者隱私。
*   **特性**: 支援熱插拔 (Hot-plugging)、使用者顯式授權機制。

### 3. VMNet 框架
提供超越基礎 NAT/橋接模式的先進網絡拓撲。
*   **功能**: 可自定義網絡參數（如 DHCP）、設定 Port Forwarding（TCP/UDP），並支援跨進程的網絡共享（透過 XPC 序列化）。

### 4. Disk Image Kit 框架 (macOS 27)
針對磁碟鏡像管理的效率優化，解決 Raw 磁碟鏡像不支援稀疏性（Sparsity）與快照昂貴的問題。
*   **關鍵技術**: 支援 Apple Sparse Image Format (ASIF) 及分層堆疊技術（Base/Cache/Overlay layers）。
*   **效能**: 透過 Overlay 實現 Copy-on-Write 快照，透過 Cache Layer 提升遠端儲存存取效能。

### 5. 自定義 VIRTIO 裝置
為 Linux 虛擬機開發高效能的自定義通訊通道。
*   **機制**: 基於 VIRTIO 標準（共享記憶體緩衝區），最小化 Host 與 Guest 間的上下文切換（Context Switches）。
*   **應用**: 適用於 ML 加速器存取、高效能加密裝置等特殊需求。

---

## 技術實作與程式碼模式

### 虛擬機自動化設定範例
```swift
// 建立配置選項
let options = VZMacGuestProvisioningOptions()
options.userName = "jane_appleseed"
options.password = securePassword // 建議從 Keychain 讀取
options.shouldEnableAutoLogin = true
options.shouldEnableRemoteLogin = true

// 設定並啟動
let startOptions = VZMacOSVirtualMachineStartOptions()
startOptions.guestProvisioning = options
virtualMachine.start(with: startOptions)
```

### 使用 Accessory Access 進行監聽
```swift
// 設定監聽器
let criteria = [AAUSBAcccessoryMatchingCriteria()] // 可指定 Vendor ID/Product ID
let manager = AAUSBAccessoryManager.shared
manager.registerListener(myListener, matching: criteria)

// 當裝置連接時的處理
func usbAccessoryDidConnect(_ accessory: AAUSBAccessory) {
    let device = VZUSBPassThroughDeviceConfiguration(usbAccessory: accessory)
    // 將裝置掛載至 VM 的 USB 控制器
}
```

---

## 開發者實用重點

1.  **安全性最佳實踐**:
    *   在處理使用者密碼時，請使用 **Keychain**、環境變數或配置文件，切勿將硬編碼（Hard-coded）密碼寫入原始碼。
2.  **VMNet 網絡持久化**:
    *   `VMNet` 物件在應用程式關閉後不會自動儲存狀態。若需保持網絡拓撲的一致性，開發者必須自行處理序列化並持久化儲存設定。
3.  **磁碟分層效能**:
    *   Disk Image Kit 雖強大，但「堆疊層數越淺效能越佳」。應避免過度複雜的層級設計，以維持最佳讀寫吞吐量。
4.  **處理權限撤銷**:
    *   `Accessory Access` 允許使用者隨時中斷裝置連接。您的應用程式必須實作 gracefully handle（優雅處理）設備脫離的事件。
5.  **客製化開發**:
    *   自定義 VIRTIO 裝置需要您同時實作 Guest 端的驅動程式（Driver）與 Host 端的邏輯（Delegate），這是針對效能敏感（Low-latency）場景的解決方案，需進行謹慎的效能分析。

透過這些新工具，開發者能夠打造出更具備生產力與彈性的虛擬化開發環境，不僅能服務開發者工作流程，也能提供高品質的桌面虛擬化體驗。

<!-- resources -->

---

## 資源連結

- [DiskImageKit](https://developer.apple.com/documentation/DiskImageKit)
- [Accessory Access](https://developer.apple.com/documentation/AccessoryAccess)
- [vmnet](https://developer.apple.com/documentation/vmnet)
- [Virtual I/O Device (VIRTIO) Version 1.4](https://docs.oasis-open.org/virtio/virtio/v1.4/virtio-v1.4.html)
- [Virtualization](https://developer.apple.com/documentation/Virtualization)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/224/5/33a91529-8caf-409e-9c54-1b8952744651/downloads/wwdc2026-224_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/224/5/33a91529-8caf-409e-9c54-1b8952744651/downloads/wwdc2026-224_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/224/
