# Meet Trust Insights

## WWDC 2026 Session Summary: Meet Trust Insights

### Session Overview
The "Meet Trust Insights" session introduces a new iOS 27 framework designed to help developers combat social engineering and coercion. Unlike traditional security measures that focus on device integrity or authentication, Trust Insights uses behavioral context—such as interaction patterns, timing, and sensor data—to detect if a user is being pressured or coached by an attacker (e.g., in tech support scams, authority impersonation, or family emergency fraud). The framework provides a privacy-first approach to identifying when a user’s "intent" may not be genuine, enabling apps to inject friction or warnings at critical moments to protect the user.

---

### Key Concepts & Frameworks
*   **Trust Insights Framework:** A client-side Swift API that evaluates the likelihood that a user is currently being coached or coerced.
*   **Behavioral Context:** The system monitors how a user interacts with the app (timing, sensor data, patterns) without ever inspecting the actual content of photos, messages, or emails.
*   **Privacy-First Architecture:** Device-sourced data never leaves the device. Only the final insight value is transmitted to Apple's servers, and inputs are discarded immediately after evaluation.
*   **Operation Categories:** To tune accuracy, developers must categorize the user's action into one of five buckets:
    *   `Payment`: Asset, money, or content exchange.
    *   `Account`: Security or account detail updates.
    *   `Application`: Resource-heavy operations (e.g., AI inference).
    *   `Communication`: Forms, messages, or signed documents.
    *   `Other`: Fallback for unique use cases.

---

### Integration Patterns & Techniques

#### 1. Configuration
*   **Entitlement:** Developers must declare the Trust Insights capability in the Xcode target settings.
*   **Parameter Packs:** Insights are requested via a parameter pack where you specify the schema and optional model versions. Specifying current/prior versions allows for **Model Governance**, enabling validation of results before wide-scale deployment.

#### 2. Evaluation Logic
*   **Asynchronous Processing:** `requestEvaluation()` is an async call that requires internet reachability and can take a few seconds; it is recommended to place this during existing UI transitions (e.g., interstitials).
*   **Handling Results:** The framework returns one of three values:
    *   `Unknown`: No evidence of risk (do not assume this means "low risk").
    *   `Some`: Potential risk; consider adding friction or extra verification.
    *   `High`: Strong evidence of risk; the user should be explicitly informed.

#### 3. Feedback Loops
*   **Real-time Consumption:** Developers *must* report how the insight was used (e.g., `UsedReducedFriction`, `UsedIncreasedFriction`, `UsedEvaluationOnly`) using `ReportConsumption`. Failure to do so may lead to rate limiting.
*   **Offline Labels:** For confirmed fraud, developers can submit feedback via a server-to-server API in the **Apple Business Register**. This helps refine the ML models globally.

---

### Practical Takeaways for Developers

*   **Do Not Use as a "Kill Switch":** Trust Insights should be part of a multi-layered security strategy. Never treat an insight as the sole determinant for blocking an action, as "Unknown" does not equate to "Safe."
*   **UX Integration:** Integrate the API at the "moments that matter"—the specific points in your app flow where money is transferred, or sensitive account information is updated.
*   **Testing:** Use Xcode build scheme overrides to simulate different insight values and error states during development. Your requests hit a sandbox environment until the app is distributed via the App Store.
*   **Privacy Stewardship:** The framework does not share device signals with Apple or third parties. Always check the authorization status before making a request, as users retain the right to disable Trust Insights in settings.
*   **Complementary Tools:** Consider pairing Trust Insights with **AppAttest** to ensure that your server-side requests are originating from an authentic, untampered instance of your app.

<!-- resources -->

---

## Resources

- [TrustInsights](https://developer.apple.com/documentation/TrustInsights)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/379/4/e12c4703-5c00-44f7-a5f8-80f6e5b7ebd5/downloads/wwdc2026-379_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/379/4/e12c4703-5c00-44f7-a5f8-80f6e5b7ebd5/downloads/wwdc2026-379_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/379/
