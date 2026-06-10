# Explore distributed inference and training with MLX

# WWDC 2026: Explore Distributed Inference and Training with MLX

## Overview
This session introduces the capabilities for scaling machine learning workloads across multiple Apple Silicon Macs using the **MLX** framework. By leveraging Thunderbolt 5 for low-latency, high-bandwidth communication, developers can now cluster multiple Macs to perform distributed Large Language Model (LLM) inference and fine-tuning. This allows for running massive models (like 1-trillion parameter LLMs) that exceed the memory capacity of a single machine and provides significant performance speedups for fine-tuning tasks, all while keeping data local and private.

---

## Key Concepts, APIs, and Frameworks

### The Distributed Stack
*   **RDMA (Remote Direct Memory Access) over Thunderbolt 5:** The foundation of the stack. Introduced in macOS 26.2, it allows memory-to-memory data transfer between machines with minimal CPU and OS overhead.
*   **JAKL (Just Another Collective Library):** An open-source library by Apple that acts as the communication backend. It provides collective communication primitives (e.g., `all-reduce`) and handles the complexities of topology management and RDMA transport.
*   **MLX:** The primary machine learning framework designed for Apple Silicon, now integrated with JAKL to orchestrate distributed jobs.
*   **MLX LM:** A high-level library built on MLX that provides out-of-the-box CLI tools for distributed chat, inference, and fine-tuning.

### Distributed Topologies
JAKL supports two primary network topologies to balance latency and bandwidth:
*   **Mesh:** Connects every machine directly to every other machine. Best for latency-sensitive tasks like **Tensor Parallelism**.
*   **Ring:** Nodes connect only to neighbors. More scalable and efficient for bandwidth-bound tasks; JAKL can automatically select the optimal configuration based on the workload.

---

## Demonstrated Techniques & Patterns

### 1. Cluster Orchestration
*   **Automatic Configuration:** Use the `mlx-distributed config` script to probe Thunderbolt connections and generate a `host.json` file.
*   **MLX Launch:** A helper utility that SSHs into cluster nodes to initiate processes. It supports environment variable injection (e.g., enabling `MLX_METAL_FAST_SYNC`) and handles model sharding automatically.

### 2. Sharding Strategies
*   **Tensor Parallelism (Width-wise):** Splits each layer across multiple machines. All nodes process the same token simultaneously. This is the default in `MLX LM` and is ideal for inference speedups, provided the network has low latency.
*   **Pipeline Parallelism (Depth-wise):** Splits the model by assigning different layers to different machines. Data moves sequentially through the cluster. This is useful for memory-constrained scenarios where models are too large for one device.

### 3. Data Parallel Training
*   Replicates the model on every Mac while sharding the training data. Each node calculates gradients locally; JAKL then performs an `all-reduce` to synchronize these gradients, resulting in a linear speedup proportional to the number of nodes.

---

## Practical Takeaways for Developers

*   **Low Friction Migration:** You can move from single-node to multi-node setups with minimal changes to your existing code. Simply prefix your execution commands with `mlx launch`.
*   **API Flexibility:** 
    *   **CLI:** Easiest for immediate experimentation and running standard models.
    *   **Python:** Recommended for custom research and fine-grained sharding control via `shard_linear` functions.
    *   **Swift/C++:** Available for embedding distributed AI capabilities directly into native applications.
*   **Performance Optimization:** For distributed tensor parallelism, **Mesh topology is critical** to ensure low-latency communication during the frequent syncs required for every layer.
*   **Privacy:** Because the cluster runs locally, your training data and model weights never leave your physical hardware, offering a massive advantage over cloud-based training for sensitive datasets.
*   **Resources:** Developers should consult the WWDC25 session *“Getting Started with MLX on Apple Silicon”* for foundational knowledge and check the official MLX documentation for advanced training loop customization.

<!-- resources -->

---

## Resources

- [MLX Swift LM on GitHub](https://github.com/ml-explore/mlx-swift-lm)
- [MLX Swift Examples](https://github.com/ml-explore/mlx-swift-examples)
- [MLX Examples](https://github.com/ml-explore/mlx-examples)
- [MLX Swift](https://github.com/ml-explore/mlx-swift)
- [MLX  LM - Python API](https://github.com/ml-explore/mlx-lm)
- [MLX Explore - Python API](https://github.com/ml-explore/mlx)
- [MLX Framework](https://mlx-framework.org)
- [MLX](https://ml-explore.github.io/mlx/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/233/4/379c319a-5718-4fd2-aac6-2f97180c5892/downloads/wwdc2026-233_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/233/4/379c319a-5718-4fd2-aac6-2f97180c5892/downloads/wwdc2026-233_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/233/
