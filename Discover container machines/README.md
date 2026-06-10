# Discover container machines

# Session Summary: Discover Container Machines

## Overview
"Discover Container Machines" introduces a new feature that bridges the gap between lightweight containerization and persistent development environments. Building upon the **Containerization framework** (open-sourced at WWDC25), Container Machines provide a highly integrated, stateful Linux environment that runs directly on macOS. The session highlights how developers can leverage these machines for cross-platform workflows—such as building and testing server-side Swift applications—without the performance overhead or complexity of traditional virtual machines.

---

## Key Concepts and Frameworks

### The Containerization Framework
Introduced in 2025, this Swift framework serves as the engine for running Linux containers on macOS. It provides:
*   **Virtual Machine-based isolation:** Ensures security and performance with sub-second startup times.
*   **Core APIs:** Handles storage, networking, execution, and a Linux init system.

### Container Machine
A new feature built on top of the containerization foundation. Its core design principles include:
*   **Fast & Lightweight:** Optimized for rapid creation and low resource consumption.
*   **Persistent:** Unlike standard containers, changes made inside a Container Machine (e.g., installing new dependencies) persist across restarts.
*   **Native Integration:** Automatically handles user mapping, file system synchronization, and networking between macOS and the Linux environment.

---

## Demonstrated Patterns and Techniques

### Lifecycle Management
The session demonstrates the **`container-tool`** CLI, which manages the lifecycle of these environments. Common patterns include:
*   **Creation:** `container-machine create --name [name] --default`
    *   By setting a machine as "default," developers avoid passing the name flag to subsequent commands.
*   **Interactive Execution:** Using `container-machine run` without arguments drops the user into an interactive Linux shell.
*   **Command Execution:** `container-machine run [command]` allows for quick, one-off execution (e.g., `uname`, `whoami`) directly from the macOS terminal.

### Cross-Platform Workflow Integration
The demo highlights a seamless feedback loop for a Vapor-based web server:
1.  **Shared Environment:** The Container Machine automatically mounts the user's home directory, allowing the developer to use **Xcode** on macOS to edit code while the Linux environment compiles it.
2.  **Network Access:** By configuring the server to listen on the machine's internal IP address, developers can test the Linux-based server using native macOS browsers (e.g., Safari).
3.  **Real-time Updates:** Because the file system is shared, changes made in macOS tools (like editing an icon in Icon Composer) are instantly reflected inside the Linux runtime without needing to re-copy files or rebuild container images manually.

---

## Practical Takeaways for Developers

*   **Remove Context Switching:** Container Machines eliminate the need for complex SSH setups or separate VM management. You can "enter" your Linux environment from anywhere in your terminal.
*   **Dependency Isolation:** Create unique Container Machines for different projects to avoid conflicting toolchains or library versions.
*   **Use Existing Skills:** Since Container Machines utilize standard **OCI images** (the same as Docker/Containerization), developers can get started using familiar base images like Alpine.
*   **Persistence is Key:** Unlike ephemeral containers, Container Machines are designed for development work. You can stop your work, shut down, and resume exactly where you left off, making it an ideal environment for iterative development.
*   **Get Started:** The tools are available via the `container-tool` repository on GitHub. Developers are encouraged to adopt these tools to simplify the transition between macOS development and Linux deployment.

<!-- resources -->

---

## Resources

- [Container](https://github.com/apple/container)
- [Containerization](https://github.com/apple/containerization)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/389/4/8dd035e7-0481-4028-b4bd-e91ba3634198/downloads/wwdc2026-389_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/389/4/8dd035e7-0481-4028-b4bd-e91ba3634198/downloads/wwdc2026-389_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/389/
