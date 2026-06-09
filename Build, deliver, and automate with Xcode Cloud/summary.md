# Build, deliver, and automate with Xcode Cloud

# WWDC 2026: Build, Deliver, and Automate with Xcode Cloud

## Session Overview
This session introduces the latest refinements to **Xcode Cloud**, Apple’s continuous integration and delivery (CI/CD) service. Designed specifically for the Apple ecosystem, Xcode Cloud automates the process of building, testing, and distributing apps. The session demonstrates how to move from local development to a scalable cloud-based workflow, specifically highlighting how developers can use Xcode Cloud to manage multi-platform projects (iOS and macOS), streamline TestFlight distribution, implement custom automation via webhooks, and manage complex dependency structures through multi-repository support.

---

## Key Concepts
*   **Ephemeral Build Environments:** Xcode Cloud runs builds on short-lived virtual machines. Source code is fetched only when a build begins and is discarded immediately upon completion, ensuring high security and no persistent storage of private code.
*   **Workflows:** The primary mechanism for defining automation. Workflows allow developers to group actions like building, testing, and archiving.
*   **Distribution Integration:** Xcode Cloud bridges the gap between development and App Store Connect by automating the creation of app records, bundle ID management, and TestFlight uploads.
*   **Event-Driven Automation (Webhooks):** Xcode Cloud supports lifecycle-based webhooks (Build Created, Started, Completed), allowing developers to pipe build data into custom dashboards or Slack/communication channels.
*   **Repository Management:** Support for multi-repository projects, allowing developers to pull shared frameworks or modularized code from different remote sources into a single build environment.

---

## Techniques & Workflow Patterns

### 1. Onboarding New Projects
*   **Report Navigator:** The primary entry point for Xcode Cloud within the Xcode interface.
*   **Cloud Tab:** Developers can trigger the "Get Started" assistant here, which automatically detects products in the workspace and handles signing/distribution mapping.

### 2. Multi-Platform Management
*   The presenter demonstrated managing both an **iOS app and a macOS app** within the same workspace.
*   **Workflow Manager:** A dedicated UI panel where developers can create and modify actions (e.g., creating an "Archive" action for TestFlight distribution) for different platforms independently.

### 3. Advanced Automation via Webhooks
*   Developers can configure webhooks by selecting **Manage Webhooks** in the Xcode Cloud menu.
*   The system provides a clear view of the "delivery history" for each hook, color-coding results (green for successful delivery) to make debugging integration issues easier.

### 4. Handling Modular Code
*   **Multi-Repository Support:** Using **Manage Repositories**, developers can link secondary, private, or shared framework repositories to their main app project. Xcode Cloud automatically pulls these dependencies during the build process, provided the developer has authorized the source provider.

---

## Practical Takeaways for Developers
*   **Offload CI/CD:** By moving unit tests and long-running build processes to the cloud, developers can free up their local machines for active feature development.
*   **Parallelization:** Xcode Cloud automatically runs tests in parallel across various device types and OS versions, catching regressions that might be missed during local-only testing.
*   **Simplified Distribution:** You no longer need to manually manage app records or provisioning profiles via the web portal for every new build; the "Setup Distribution" assistant automates the configuration of SKUs and bundle identifiers directly from Xcode.
*   **Scalability:** As your project grows from a single app to a modularized codebase with shared frameworks, use the repository management tools to ensure your build environment stays synced with your architecture.
*   **Stay Informed:** Use the webhook lifecycle events to notify your team, update custom dashboards, or trigger additional post-build automations to maintain high development velocity.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/261/7/35c49f2b-3f0a-4956-826b-d54d9fed678e/downloads/wwdc2026-261_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/261/7/35c49f2b-3f0a-4956-826b-d54d9fed678e/downloads/wwdc2026-261_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/261/
