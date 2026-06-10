# Design intuitive search experiences

# WWDC 2026: Design Intuitive Search Experiences

## Session Overview
This session provides a comprehensive design framework for implementing search across Apple platforms (iOS, iPadOS, and macOS). The focus is on creating "effortless" search experiences by leveraging standardized components, thoughtful placement, and contextual navigation patterns. Designers and developers are encouraged to prioritize ergonomics—specifically keyboard reachability on mobile and spatial efficiency on larger displays—to reduce friction when users seek to find, navigate, or discover content.

---

## Key Concepts and Design Patterns

### 1. The Search Field Component
Apple provides a standard search component that includes:
*   **Leading magnifying glass icon:** A universal, required element for recognition.
*   **Placeholder text:** Clearly defines scope (e.g., "Search Albums" vs. "Search Music").
*   **Clear and Cancel buttons:** Essential for input management and exiting the search flow.
*   **Automatic Styling:** The field adapts its visual presentation (e.g., "glass" styling in toolbars vs. standard styling in content regions).

### 2. Strategic Placement
*   **iOS Toolbar:** Placing search in a bottom toolbar optimizes for ergonomics by animating the field upward over the keyboard.
*   **iOS Inline/Top:** Used when bottom space is occupied (e.g., by a sheet).
*   **Dedicated Search Tab:** Recommended for complex, multi-section apps. A "standard tab" allows for a landing page with suggestions/categories, while a "prominent tab" acts as an immediate shortcut to the keyboard.
*   **Scope-Specific Search:** Placing search directly in a specific view (e.g., within an album list) to signal narrowed scope.
*   **iPad/Mac Patterns:** Search is best placed in the trailing toolbar for split-view navigation, the top of a sidebar, or as a primary tab.

---

## Techniques and Best Practices

### Improving Discovery and Input
*   **Recent Searches:** Display these immediately upon focusing the search field. Allow users to remove items via swipe-to-delete or "Clear All" headers.
*   **Predictive Suggestions:** Provide real-time auto-completions. Visually distinguish user input from the predicted text to maintain orientation.
*   **Constraint:** Limit the number of suggestions to ensure actual search results remain visible.

### Refining and Filtering
*   **Scope Bar:** A UI control for lightweight switching between categories or locations (e.g., "Current Mailbox" vs. "All Mailboxes").
*   **Contextual Filters:** Use robust filter menus that adapt based on the search context (e.g., Maps offering "restaurants" or "trails" based on the viewport/intent).
*   **Search Tokens:** Enable users to filter by specific criteria (person, place, date) using natural language. Tokens live within the search field but should be paired with other visible UI filters since they are not always discoverable.

### Handling Failures
*   **Content Unavailable View:** Do not leave the screen blank. Use the standard "content unavailable" configuration to display a search icon, a clear title, and a subtitle explaining that no results were found. Include the original search query so users can check for typos.

---

## Practical Takeaways for Developers

*   **Maintain Consistency:** While custom branding is allowed, keep core components (the search symbol, the clear button) consistent with system conventions so users don't have to "re-learn" how to search.
*   **Prioritize Scope:** Before coding, identify *what* the user is searching. If the search scope is limited to a single sub-menu, place the search field inline within that view rather than using a global search tab.
*   **Think Globally, Act Locally:** Use the Human Interface Guidelines (HIG) for visual alignment, but always test the interaction flow:
    *   *Does the search field animate correctly relative to the keyboard?*
    *   *Is it clear what the user is searching?*
    *   *Are the most common search paths (recents/suggestions) reducing the need for full text input?*
*   **Documentation:** Refer to the [Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/) for specific padding, typography, and standard symbol guidance.

<!-- resources -->

---

## Resources

- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/292/5/05adbfdf-d9ba-4a6d-8d2f-f43593907f55/downloads/wwdc2026-292_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/292/5/05adbfdf-d9ba-4a6d-8d2f-f43593907f55/downloads/wwdc2026-292_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/292/
