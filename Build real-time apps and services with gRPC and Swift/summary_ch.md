# Build real-time apps and services with gRPC and Swift

這是一份關於 WWDC 2026 Session「Build real-time apps and services with gRPC and Swift」的技術摘要。

---

# WWDC 2026 技術摘要：使用 gRPC 與 Swift 建構即時應用程式

## 概述
本 Session 深入探討如何利用 **gRPC Swift** 來簡化客戶端與伺服器之間的通訊。gRPC 提供了一套高效、基於合約（Contract-based）的通訊模式，透過 Protocol Buffers (Protobuf) 定義服務介面，自動生成型別安全的 Swift 程式碼。這不僅大幅減少了手動處理網路請求的複雜度，還能透過串流（Streaming）機制實現即時（Real-time）的互動體驗。

---

## 主要概念與技術架構

### 1. 什麼是 gRPC？
*   **Remote Procedure Calls (RPC)：** gRPC 允許客戶端直接呼叫遠端伺服器上的函式，而非傳統的 HTTP/REST 風格。
*   **源頭即真理（Source of Truth）：** API 定義在 `.proto` 檔案中，服務定義與實作解耦。
*   **Protobuf：** 一種二進位格式，相比 JSON，體積更小且解析速度更快，非常適合頻寬受限的移動端環境。

### 2. 關鍵組件
*   **gRPC Swift Neo Transport：** 基於高效能的 Swift NIO 函式庫，提供底層網路層支援。
*   **Swift Protobuf：** 提供 Build Plugin，將 `.proto` 檔案自動轉換為 Swift 的 Struct 與 Client 程式碼。

### 3. RPC 通訊類型
*   **Unary RPC：** 單一請求對應單一回應（最常見的模式）。
*   **Client Streaming：** 客戶端持續發送訊息，伺服器發送單一回應。
*   **Server Streaming：** 客戶端發送單一請求，伺服器持續推播回應。
*   **Bidirectional Streaming：** 雙向串流，雙方皆可隨時發送或接收多個訊息，適用於高互動性場景。

---

## 程式碼模式與實作技術

### 定義服務 (Protobuf)
在 `.proto` 檔案中定義 Service 與 Message。使用 `stream` 關鍵字宣告串流 RPC：
```protobuf
service CartService {
  rpc FollowRace(stream FollowRequest) returns (stream RaceEvent);
}
```

### Xcode 整合
1.  **Dependency：** 加入 `gRPC Swift` 與 `Swift Protobuf` 套件。
2.  **Build Phase：** 在 "Run Build Tool Plugins" 加入 gRPC Protobuf Generator。
3.  **Config：** 使用 JSON 配置文件來控制僅生成 Client 端程式碼（若為 iOS App，通常不需產生 Server 端代碼）。

### 實作 Client (Swift)
建議將 gRPC Client 封裝在 Manager 中並注入 Environment，避免重複連線導致延遲：
```swift
// 透過 Task 呼叫
try await withThrowingTaskGroup(of: Void.self) { group in
    // 使用非同步串流處理請求與回應
    let stream = client.followRace(requestStream: requestAsyncStream)
    for try await response in stream {
        // 更新 UI 狀態
    }
}
```

### 伺服器部署 (Cloud)
*   **容器化：** 使用多階段建構（Multi-stage build）將 Swift 二進位檔案打包至 Swift Slim Runtime Image，大幅縮減映像檔大小。
*   **部署：** 利用 `gcloud run deploy` 將服務部署至雲端，並啟用 HTTP/2 與 TLS 以確保通訊安全。

---

## 開發者實用重點

*   **優化效能：** 不要為每個 View 建立新的 Client，應將 Client 實例化並透過 `EnvironmentObject` 或 `Dependency Injection` 在 View 之間共享，以複用 TCP 連線。
*   **生命週期管理：** 當 App 進入背景（Background）時，應手動斷開或暫停 gRPC 串流以節省系統資源與電量。
*   **錯誤處理：** 由於網路環境不穩定，務必在 `do-catch` 區塊中處理連線中斷或逾時問題。
*   **雙向串流的威力：** 針對需要頻繁更新資料的場景（如即時賽事計分板、地圖位置追蹤），雙向串流可顯著降低延遲，並比不斷輪詢（Polling）更省電。
*   **開發資源：** 可參考 [gRPC Swift GitHub Repository](https://github.com/grpc/grpc-swift) 獲取範例程式與教學，該專案為開源，鼓勵開發者提交 Issue 或貢獻代碼。

---
*總結：gRPC 是建構高效能 Swift 服務的強大工具。透過自動產生的程式碼與強型別檢查，開發者能更專注於業務邏輯而非底層的傳輸協議。*

<!-- resources -->

---

## 資源連結

- [About gRPC](https://grpc.io/)
- [gRPC Swift Extras](https://github.com/grpc/grpc-swift-extras)
- [gRPC Swift Protobuf](https://github.com/grpc/grpc-swift-protobuf)
- [gRPC Swift NIO Transport](https://github.com/grpc/grpc-swift-nio-transport)
- [gRPC Swift](https://github.com/grpc/grpc-swift)
- [Swift on Server](https://www.swift.org/server/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/265/4/05249c6d-4136-4164-a8d0-5db0bbb22c7f/downloads/wwdc2026-265_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/265/4/05249c6d-4136-4164-a8d0-5db0bbb22c7f/downloads/wwdc2026-265_sd.mp4?dl=1)

**Session 頁面：** https://developer.apple.com/videos/play/wwdc2026/265/
