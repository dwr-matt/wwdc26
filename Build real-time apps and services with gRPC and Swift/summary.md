# Build real-time apps and services with gRPC and Swift

## Session Summary: Build real-time apps and services with gRPC and Swift

### Overview
This session introduces **gRPC Swift**, a high-performance, open-source framework for building real-time, service-oriented applications. The session demonstrates how to move away from manually crafting networking code by using **Protocol Buffers (Protobuf)** as a source of truth to automatically generate type-safe client and server code. By leveraging gRPC’s built-in support for streaming and efficient binary serialization, developers can create responsive, data-efficient mobile applications that communicate seamlessly with backend services.

---

### Key Concepts, APIs, and Frameworks
*   **gRPC:** An industry-standard framework for Remote Procedure Calls (RPCs) that defines services as functions with specific inputs and outputs rather than HTTP endpoints.
*   **Protocol Buffers (Protobuf):** The interface definition language (IDL) used to define service APIs and message structures. Protobuf provides binary serialization, which is roughly half the size of JSON, significantly optimizing mobile data usage.
*   **gRPC Swift:** The core library providing runtime components, including the `gRPC Swift Neo Transport` (built on SwiftNIO) for high-performance networking and `Swift Protobuf` for code generation.
*   **RPC Types:**
    *   **Unary:** Single request, single response.
    *   **Client Streaming:** Client streams multiple messages; server returns a single response.
    *   **Server Streaming:** Client sends a single request; server streams multiple responses.
    *   **Bi-directional Streaming:** Both client and server stream messages continuously.

---

### Code Patterns and Techniques
*   **Code Generation:** The session emphasizes using the **gRPC Protobuf build plugin** in Xcode. By adding a JSON configuration file, developers can target specifically what code to generate (e.g., omitting server-side code for iOS clients).
*   **Connection Management:** 
    *   **Lazy Initialization:** Initialize gRPC clients only when needed to save resources.
    *   **Environment Propagation:** Share a single gRPC client instance across multiple SwiftUI views via the `@Environment` to prevent redundant connections and reduce latency.
    *   **Lifecycle Awareness:** Monitor `scenePhase` to disconnect clients when the app moves to the background, freeing up system resources.
*   **Streaming Implementation:**
    *   **Task Groups:** Used in the server to manage concurrent data streams efficiently.
    *   **Async Sequences:** Used to handle incoming request streams and iterate over server response streams.
    *   **Bidirectional Sync:** The session demonstrates mapping state changes (like toggling a leaderboard) into an `AsyncStream` to send real-time updates to the server while simultaneously consuming data.
*   **Cloud Deployment:** The session covers packaging a Swift server into a container using a **multi-stage Docker build** (using `Swift Slim` runtime images to reduce image size) and deploying to cloud platforms via `gcloud` (or similar providers) using HTTP/2 and TLS.

---

### Practical Takeaways for Developers
1.  **Use Protobuf as the Source of Truth:** Avoid hand-writing networking code. Define your API in a `.proto` file and let the build tool generate the interfaces for you to ensure consistency between the client and server.
2.  **Optimize for Mobile:** Use gRPC's binary format to minimize payload sizes, which is essential for maintaining performance under poor network conditions.
3.  **Leverage First-Class Streaming:** For live dashboards, chat features, or real-time tracking, implement bidirectional streaming rather than polling, which is more resource-intensive and prone to latency.
4.  **Prioritize Connection Reusability:** Do not create a new client per view. Implement a `ClientManager` to maintain long-lived, reusable connections throughout the app's lifecycle.
5.  **Get Started via Open Source:** The gRPC Swift project is open-source and hosted on GitHub. Developers are encouraged to use the provided tutorials and contribute to the framework by improving documentation or proposing new features.

<!-- resources -->

---

## Resources

- [About gRPC](https://grpc.io/)
- [gRPC Swift Extras](https://github.com/grpc/grpc-swift-extras)
- [gRPC Swift Protobuf](https://github.com/grpc/grpc-swift-protobuf)
- [gRPC Swift NIO Transport](https://github.com/grpc/grpc-swift-nio-transport)
- [gRPC Swift](https://github.com/grpc/grpc-swift)
- [Swift on Server](https://www.swift.org/server/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/265/4/05249c6d-4136-4164-a8d0-5db0bbb22c7f/downloads/wwdc2026-265_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/265/4/05249c6d-4136-4164-a8d0-5db0bbb22c7f/downloads/wwdc2026-265_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/265/
