# Build AI-powered scripts with the fm CLI and Python SDK

# WWDC 2026: Build AI-powered scripts with the `fm` CLI and Python SDK

## Session Overview
This session introduces new, non-Swift-exclusive interfaces for Apple’s Foundation Models on macOS 27 and iOS 27. Building upon the Foundation Models Framework introduced at WWDC25, Apple is expanding accessibility by providing a native command-line tool (`fm`) and a dedicated Python SDK. These tools allow developers to integrate on-device and private cloud-based LLMs into shell scripts, automation workflows, and data science pipelines without needing to rebuild their Xcode projects.

---

## Key Concepts, APIs, and Frameworks

### 1. The `fm` CLI Tool
Pre-installed on macOS 27, the `fm` CLI provides terminal-based access to on-device models and Private Cloud Compute models.
*   **`fm chat`**: An interactive interface for rapid experimentation and prompt testing.
*   **`fm respond`**: Designed for scripting, this command executes a single prompt and outputs the raw result. It supports structured schemas and image inputs.
*   **`fm schema object`**: A helper command used to define the desired JSON output structure for guided generation.

### 2. The Foundation Models Python SDK
A new SDK that brings core framework features (tool calling, guided generation) to the Python ecosystem.
*   **Requirements**: macOS 27, Apple Silicon, Xcode, and Python 3.10+.
*   **Workflow**: Provides a familiar API for developers accustomed to the Swift framework but allows leveraging the massive Python data science ecosystem (Pandas, Matplotlib, Jupyter).

### 3. Core Features (Consistent Across All Interfaces)
*   **Guided Generation**: Using schemas to force the model to return structured data (JSON).
*   **Tool Calling**: Enabling the model to interact with external code/context.
*   **Model Flexibility**: Seamless switching between the lightweight, free, on-device model and more powerful Private Cloud Compute models.

---

## Demonstrated Techniques & Code Patterns

### Automation with `fm`
The session demonstrated a script to organize file directories. By piping directory listings into `fm respond` with a defined JSON schema, the script could reliably categorize files into "drafts" and "finals" regardless of messy filenames.
*   **Pattern**: `fm schema object --json-schema schema.json > my_schema.json` followed by `fm respond --prompt "sort these" --schema my_schema.json`.

### Evaluation Pipelines in Python
Using a Jupyter Notebook, developers can iterate on prompts much faster than in Xcode:
1.  **Data Generation**: Use a large server model to create an evaluation dataset (inputs + expected outputs).
2.  **Implementation**: Create multiple variations of a prompt (minimal, descriptive, rule-based).
3.  **Grading**: Run the `fm` Python SDK to generate responses, then use a "judge" LLM to score the results against criteria.
4.  **Analysis**: Use **Pandas** to aggregate results and **Matplotlib** to visualize error rates, hallucination frequency, and token usage, allowing for data-driven prompt engineering.

---

## Practical Takeaways for Developers

*   **Rapid Prototyping**: Stop recompiling your Xcode project just to test a prompt. Use `fm chat` for quick experiments or the Python SDK in a Jupyter notebook for rapid iteration.
*   **Automate Local Tasks**: Use the `fm` CLI to build "agentic" shell scripts that can parse logs, organize files, or perform data extraction tasks on your local machine using the privacy-first on-device model.
*   **Data-Driven AI Development**: Move away from "gut-feeling" prompt engineering. Build evaluation pipelines in Python to quantify the performance of your prompts, ensuring that changes actually improve model accuracy.
*   **Leverage Existing Ecosystems**: Since the SDK is native Python, you can integrate AI responses directly into existing machine learning workflows, data visualization tools, and testing frameworks.
*   **Accessibility**: No API keys are required for the on-device model, and it carries no cloud costs, making it ideal for background automation and development-phase testing.

<!-- resources -->

---

## Resources

- [Foundation Models SDK for Python on GitHub](https://github.com/apple/python-apple-fm-sdk)
- [Foundation Models SDK for Python Documentation on GitHub](https://apple.github.io/python-apple-fm-sdk/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/334/4/65b71eea-f323-4f86-9096-889b6da91bdd/downloads/wwdc2026-334_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/334/4/65b71eea-f323-4f86-9096-889b6da91bdd/downloads/wwdc2026-334_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/334/
