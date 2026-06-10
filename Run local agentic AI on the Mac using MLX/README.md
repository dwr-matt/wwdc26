# Run local agentic AI on the Mac using MLX

# WWDC 2026: Run Local Agentic AI on the Mac using MLX

## Session Overview
This session introduces a robust, local-first stack for building and running agentic AI workflows on Apple Silicon. The MLX team demonstrates how developers can move beyond simple chat interactions to fully autonomous agents capable of managing files, executing terminal commands, hitting APIs, and performing complex development tasks—all without data leaving the machine. By utilizing the MLX framework, developers can achieve high-performance, cost-effective AI agents that leverage local hardware accelerators and distributed computing.

---

## Key Concepts, APIs, and Frameworks

### The Four-Layer Stack
The session defines a clear architectural stack for local agentic AI:
1.  **MLX (Foundation):** An open-source array framework purpose-built for Apple Silicon, handling low-level computation, Metal acceleration, and memory management.
2.  **MLX LM (Language Models):** A library providing tools to load, quantize, and fine-tune large language models (LLMs) from sources like Hugging Face.
3.  **MLX LM Server:** An OpenAI-compatible HTTP server that exposes local models via a standard API. It supports **structured tool calling** and reasoning models, serving as a drop-in replacement for cloud-based LLM APIs.
4.  **Agent Layer:** The top-level application (e.g., Xcode, OpenCode, or custom scripts) that utilizes the chat completion protocol to manage the agentic loop.

### Hardware & Performance Technologies
*   **Neural Accelerators (M5 Chip):** MLX automatically targets these to provide up to 4x faster matrix multiplication and attention kernel processing, significantly speeding up prompt processing—the bottleneck in long agentic loops.
*   **Continuous Batching:** A technique in `MLX LM Server` that dynamically groups incoming sub-agent requests, allowing them to run concurrently on the GPU without waiting for a serial queue.
*   **Distributed Inference:** Enables sharding large models (like the 1.6T parameter DeepSeq) across multiple Macs via Thunderbolt or Ethernet. Support for **Thunderbolt RDMA** (macOS 26.2+) enables low-latency, high-bandwidth communication, delivering up to 3x speedups in distributed setups.

---

## Code Patterns & Techniques
*   **The Agentic Loop:** Developers should structure agents to cycle through a "Plan-Act-Observe" workflow: the model reasons, calls a tool, observes the output, and repeats until the task is complete.
*   **Local Server Integration:** To connect an agent, developers simply point the agent's base URL to `localhost` and specify the local model name. No complex refactoring is required.
*   **Distributed Configuration:** Scaling models is handled via a `host` file passed to `mlx_launch`. The framework automatically shards model weights across the cluster.
*   **Development Workflow Integration:** The session demonstrated pointing Xcode’s internal "Intelligence" tab to a local `MLX LM Server` port (e.g., 8080), allowing the IDE to offload file analysis and bug-fixing tasks to local hardware.

---

## Practical Takeaways for Developers
*   **Privacy & Cost:** Moving agents to local hardware ensures zero usage costs and keeps sensitive codebase data strictly on the developer's machine.
*   **Interoperability:** Because `MLX LM Server` uses the OpenAI API standard, existing agents and tools like Ollama or LM Studio are compatible by default.
*   **Iterative Development:** Agents are not just for research; they are effective in IDEs for tasks like building projects from scratch, running compilation commands, and performing targeted bug fixes.
*   **Ease of Adoption:** Setting up the environment is a three-step process: 
    1. `pip install mlx-lm`
    2. Start the server: `mlx_lm.server --model [model_name]`
    3. Point your agent framework URL to `localhost`.
*   **Optimization:** You do not need to manually optimize for hardware; MLX handles kernel selection for M-series chips automatically, ensuring maximum efficiency without changing application code.

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
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/232/4/f309be4a-8e5b-4c0f-843a-fcbd84c5e2d1/downloads/wwdc2026-232_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/232/4/f309be4a-8e5b-4c0f-843a-fcbd84c5e2d1/downloads/wwdc2026-232_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/232/
