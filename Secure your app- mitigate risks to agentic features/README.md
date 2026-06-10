# Secure your app: mitigate risks to agentic features

# WWDC 2026: Secure Your App – Mitigate Risks to Agentic Features

## Session Overview
This session focuses on the security challenges introduced by "agentic" applications—apps that utilize Large Language Models (LLMs) to process context and execute actions on behalf of the user. The speakers, Willy and Akshay, highlight that while LLMs provide powerful automation, they also introduce a probabilistic attack surface. The session provides a framework for threat modeling these applications, specifically addressing **Indirect Prompt Injection**, data poisoning, and action poisoning. The speakers emphasize shifting from reactive patches to deterministic security guardrails within the **Foundation Models** and **App Intents** frameworks.

---

## Key Concepts, APIs, and Frameworks

### 1. Agentic Risks
*   **Indirect Prompt Injection:** Occurs when malicious instructions are embedded in "extra context" (e.g., calendar invites, social media feeds, emails) that the LLM processes. This can redirect control flow or trick the model into performing unauthorized actions.
*   **Data Poisoning:** An attacker influences the parameters of an action (e.g., changing a "send message to Mom" request to send to an attacker instead).
*   **Action Poisoning:** An attacker steers the LLM to execute a malicious or unintended action (e.g., opening a malicious URL instead of summarizing an email).
*   **The "Lethal Trifecta":** A risk condition where a system has access to private data, exposure to untrusted content, and the ability to perform external actions.

### 2. Frameworks
*   **Foundation Models Framework:** Used for custom agents; provides granular control over the agentic loop through lifecycle modifiers.
*   **App Intents Framework:** Used for system-level integrations (Siri, Spotlight). Security is managed here via built-in risk-based confirmation systems and schema-level policies.

---

## Code Patterns & Techniques

### Foundation Models Framework: Lifecycle Event Modifiers
These callbacks act as deterministic checkpoints during the agent’s execution loop:
*   **`on-tool-call` modifier:** Intercepts tool calls before they execute. By throwing an error here, you can halt execution, making it the ideal location to force **user confirmation** for high-risk actions (e.g., financial transactions).
*   **`history transform` modifier:** Intercepts the transcript before inference. This allows developers to:
    *   **Spotlighting:** Add tags (e.g., `<untrusted>`) around data sources like public feeds to alert the model that the content is suspect.
    *   **Redaction:** Strip out sensitive PII before the context reaches the LLM.

### App Intents: Built-in Guardrails
*   **Risk-Based Confirmations:** The system automatically assesses the "risk metadata" of an intent based on its side effects (e.g., deleting data, exfiltrating info). If risk is high, the system forces a user prompt before execution.
*   **Authentication Policy:** Prevents unauthorized execution from the lock screen.
    *   Developers can use the `authenticationPolicy` property to mandate `requiresAuthentication`.
    *   When adopting an intent schema, the app inherits the schema’s default security policy; developers can override these only to make them *stricter*.

---

## Practical Takeaways for Developers

1.  **Perform Data Flow Analysis:** Map out every input source entering your LLM. Identify any external data (calendars, web feeds, user-generated content) as "untrusted."
2.  **Audit Action Side Effects:** Classify your tools by risk level. Ask: *Does this tool perform a financial transaction? Does it delete data? Can it post to the web?* High-risk tools must require human confirmation.
3.  **Favor Deterministic Mitigations:** While LLMs are probabilistic, your security logic shouldn't be. Use hard-coded checks (like the `on-tool-call` modifier) rather than relying on the LLM to "figure out" if a request is malicious.
4.  **Adopt a "Deny by Default" Mindset:** For lock screen interactions, explicitly configure your App Intents to `requiresAuthentication` if they touch sensitive user data or system state.
5.  **Use Spotlighting:** Treat all external data as potentially injected. Use structural markers (delimiters) to inform the LLM about the origin of the data it is processing.

<!-- resources -->

---

## Resources

- [Security Overview](https://developer.apple.com/security/)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/347/4/07cdbfeb-280a-49e3-aeba-c18fbb0d32b4/downloads/wwdc2026-347_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/347/4/07cdbfeb-280a-49e3-aeba-c18fbb0d32b4/downloads/wwdc2026-347_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/347/
