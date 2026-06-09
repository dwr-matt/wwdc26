# What’s new in Wallet

這是一份為開發者整理的 WWDC 2026 **「What’s new in Wallet」** 技術摘要，涵蓋了 iOS 27 中 Wallet 與票券系統的重大更新，以及全新的開發工具鏈。

---

# WWDC 2026 技術摘要：What’s new in Wallet

## 概述
本場 Session 介紹了 iOS 27 中 Wallet 票券（Passes）的視覺革新與功能增強。重點在於全新的 **Post-generic** 視覺樣式、對多種常見條碼格式的支援、以及全新的「特色行動」（Featured Actions）API。此外，為了提升開發效率，Apple 推出了視覺化設計工具 **Pass Designer** 以及伺服器端的 Swift 套件 **Pass Builder**，實現了從設計到大規模分發的一站式開發流程。

---

## 主要概念與 API 更新

### 1. 全新票券樣式：Post-generic
`post-generic` 樣式旨在讓票券具備更強的視覺衝擊力，非常適合會員卡、集點卡等場景。
*   **組成要素**：包含背景圖、主 Logo、頁首欄位（Header）、主要欄位（Primary）、頁尾欄位（Footer）及條碼。
*   **實作方法**：在 `pass.json` 中設定 `"style": "post-generic"`。
*   **向後相容**：為了支援 iOS 26 及更舊版本，建議同時定義 `generic` 與 `post-generic` 樣式。

### 2. 新增條碼類型
iOS 27 新增支援四種常見的條碼格式：**EAN 13、Code 39、Codabar、ITF**。
*   **最佳實踐**：由於舊版本 iOS 不支援新格式，建議在 `barcodes` 陣列中提供多個條碼，並按優先順序排列。系統會自動根據設備能力選擇可掃描的格式，避免舊版設備無法顯示條碼。

### 3. 特色行動 (Featured Actions)
過去僅限於活動票券的語意化行動，現在開放給所有樣式的票券。
*   **功能**：在票券下方顯示最多兩個自定義行動（如查看優惠、查詢狀態）。
*   **實作**：在 `pass.json` 中定義 `featuredActions` 陣列，包含 `id`、`type` 及對應的 URL。

---

## 開發工具介紹

### Pass Designer (macOS App)
這是一個所見即所得（WYSIWYG）的視覺設計器。
*   **功能**：直接在左側編輯欄位、圖片、條碼配置，右側即時預覽。
*   **輸出**：生成 `.pkpasstemplate` 檔案，可作為後端自動化生產的模板。

### Pass Builder (Swift on Server)
這是專為伺服器端設計的 Swift 套件，用於自動化生產與簽署票券。
*   **跨平台**：支援 macOS 與 Linux。
*   **自動化流程**：
    1. 載入模板與資料。
    2. 使用 `PassPackage` 類別動態配置欄位、背景圖與條碼。
    3. 使用 `PassSigner` 載入簽署憑證（Pass Signing Certificate + WWDR Intermediate）。
    4. 自動處理 manifest 生成、簽署與壓縮為 `.pkpass` 檔案。
*   **多語言支援**：透過 Swift-Java 互操作性或 Protobuf 定義，開發者可以在其他後端語言（如 Java）中調用這些功能。

---

## 程式碼模式：使用 Pass Builder 進行自動化簽署

```swift
// 1. 初始化 Pass 簽署器
let signer = PassSigner(certificates: [signingCert, wwdrCert])

// 2. 建立並配置票券
let package = PassPackage(template: myTemplate)
package.passFields.setValue("Finley", forKey: "dogName")
package.backgroundImage = PassImage(url: dogPhotoURL)

// 3. 設定條碼
package.barcode = PassBarcode(format: .pdf417, message: membershipID)

// 4. 生成並簽署
try signer.signPass(package, to: outputURL)
```

---

## 開發者實用重點建議

1.  **條碼備案策略**：若無法使用多種條碼格式，請務必將「憑證 ID」明顯地放在 Header 或 Primary 欄位中，以便店員進行手動輸入，避免掃描失敗時發生無法進入場地的窘境。
2.  **善用 Featured Actions**：雖然最多可放兩個動作，但請優先選擇對使用者當下最有意義的內容（如「查看會員福利」），並依重要性排序。
3.  **模板化生產**：不要手動建立每個票券。使用 `Pass Designer` 建立基礎結構，再交由後端的 `Pass Builder` 根據資料庫內容動態生成，能大幅減少錯誤並提升分發效率。
4.  **遷移建議**：如果您的服務已運行多年，請檢查 `pass.json` 的兼容性配置，確保在採用新 `post-generic` 風格時，舊版本 iOS 用戶仍能獲得基礎功能。

---
*更多詳細資訊請參考官方提供的 [Wallet Passes 文件](https://developer.apple.com/documentation/walletpasses) 以及 Pass Builder 的 GitHub 參考實作。*