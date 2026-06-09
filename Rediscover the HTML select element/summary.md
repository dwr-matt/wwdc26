# Rediscover the HTML select element

# Session Summary: Rediscover the HTML `<select>` element

## Overview
This session introduces "Customizable Select," a major evolution for the HTML `<select>` element. Historically, developers were forced to choose between the native, non-stylable `<select>` element (which is accessible but visually rigid) or building custom dropdowns from scratch using `div` elements and heavy JavaScript (which often breaks accessibility). The new customizable select allows developers to retain the semantic power, keyboard navigation, and screen reader compatibility of the native element while fully customizing its appearance, structure, and internal content using CSS and HTML.

---

## Key Concepts and APIs

*   **Customizable Select:** A standard-compliant way to override the default browser UI for dropdowns.
*   **`appearance: base-select;`:** The core CSS property that opts a `<select>` element out of the native browser rendering, enabling deep customization of the button and menu parts.
*   **Shadow Parts and Pseudo-classes:**
    *   `::picker-icon`: Used to style or replace the dropdown arrow/chevron.
    *   `::picker-select`: Used to style the dropdown menu container.
    *   `::check-mark`: Used to customize the indicator for the selected option.
    *   `:open`: A pseudo-class to apply styles to the button/menu when the dropdown is expanded.
*   **`selectedcontent` element:** A new specialized element placed inside the `<select>` that automatically reflects the rich content (icons, images, labels) of the currently chosen `<option>`.
*   **Progressive Enhancement:** Because this is an extension of the semantic `<select>` element, websites remain functional in older browsers that do not support these features, reverting to the native platform-specific dropdown.

---

## Code Patterns and Techniques

### 1. Basic Customization
To style the button and icon, apply `appearance: base-select;` to the `<select>`. You can then manipulate the icon directly:
```css
select {
  appearance: base-select;
  /* Add custom padding, borders, etc. */
}

select::picker-icon {
  content: url('custom-arrow.svg');
  width: 20px;
}
```

### 2. Styling the Menu
To remove the default menu styling and apply custom layouts (like CSS Grid):
```css
/* Opt-out of native menu */
select::picker-select {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 10px;
}
```

### 3. Rich Content in Options
Developers can now place non-text content, such as SVGs or images, directly inside an `<option>` element. To ensure the selected content displays properly in the main button (rather than just text), use the `selectedcontent` element:
```html
<select>
  <button>
    <selectedcontent></selectedcontent>
  </button>
  <option>
    <img src="icon.svg" alt="">
    <span>Category Name</span>
  </option>
</select>
```

---

## Practical Takeaways for Developers

*   **Prioritize Semantics:** Stop building custom `div`-based dropdowns. Customizable select provides native accessibility, focus management, and keyboard support out of the box, reducing technical debt.
*   **Progressive Enhancement is Automatic:** By using the native `<select>` as the base, you guarantee that your UI remains usable for users on older browsers or non-supported environments.
*   **Think Beyond Text:** You are no longer limited to string values. Use grids, images, and complex layouts to make your selection UI more intuitive and visually appealing.
*   **Accessibility First:** Since this remains a standard form control, it works with screen readers natively. However, ensure that any custom content added to `<option>` tags (like icons) is properly hidden from screen readers (using `alt=""` or `aria-hidden`) if the text label already provides sufficient context.
*   **Availability:** This feature is arriving in Safari 27 and Chrome 135. Developers can test it today via **Safari Technology Preview** or **Safari Beta**.

<!-- resources -->

---

## Resources

- [WebKit.org - Example website demonstrating Customizable Select](https://webkit.org/demos/customizable-select/)
- [WebKit.org - CSS Grid Lanes Field Guide](https://gridlanes.webkit.org)
- [WebKit.org – Report issues to the WebKit open-source project](https://bugs.webkit.org)
- [Submit feedback](http://feedbackassistant.apple.com)
- [HD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/315/4/f3bd9835-9ced-4f6a-a0f1-655000972674/downloads/wwdc2026-315_hd.mp4?dl=1)
- [SD Video](https://devstreaming-cdn.apple.com/videos/wwdc/2026/315/4/f3bd9835-9ced-4f6a-a0f1-655000972674/downloads/wwdc2026-315_sd.mp4?dl=1)

**Session page:** https://developer.apple.com/videos/play/wwdc2026/315/
