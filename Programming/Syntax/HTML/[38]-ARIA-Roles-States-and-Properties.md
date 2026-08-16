[Previous](./[37]-Accessibility-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[39]-Keyboard-Navigation-and-Focus.md)

*Accessibility (a11y)*

# Lesson 38 - ARIA Roles, States & Properties

## 38.1 ARIA Roles

**ARIA** (Accessible Rich Internet Applications) is a set of attributes that add extra accessibility information HTML alone doesn't provide — most often needed for custom, JavaScript-driven widgets that don't have a native HTML equivalent.

The `role` attribute tells assistive technology what a generic element is functioning as.

```html
<div role="button" tabindex="0">Click me</div>
<div role="alert">Your changes have been saved.</div>
```

---

## 38.2 ARIA States and Properties

States and properties describe an element's current condition, prefixed with `aria-`.

```html
<button aria-expanded="false" aria-controls="menu">Menu</button>
<ul id="menu" hidden>
  <li>Item 1</li>
  <li>Item 2</li>
</ul>

<input type="text" aria-label="Search products">
<input type="checkbox" aria-checked="true">
```

- `aria-expanded` tells screen readers whether a collapsible section is open.
- `aria-label` provides an accessible name when there's no visible text label.
- `aria-hidden="true"` hides purely decorative content from assistive technology while keeping it visible on screen.

---

## 38.3 The First Rule of ARIA

The most important rule of ARIA is: **don't use ARIA if a native HTML element already does the job.**

```html
<!-- Avoid -->
<div role="button" tabindex="0" onclick="submit()">Submit</div>

<!-- Prefer -->
<button onclick="submit()">Submit</button>
```

A native `<button>` is focusable, keyboard-operable, and announced correctly by default — recreating all of that with ARIA is extra work that's easy to get wrong. Reach for ARIA only when you're building something genuinely custom, like a tab panel or a combobox, that has no native equivalent.

[Previous](./[37]-Accessibility-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[39]-Keyboard-Navigation-and-Focus.md)
