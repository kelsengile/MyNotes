[Previous](./[38]-ARIA-Roles-States-and-Properties.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[40]-Accessible-Forms-Images-and-Media.md)

*Accessibility (a11y)*

# Lesson 39 - Keyboard Navigation & Focus Management

## 39.1 The `tabindex` Attribute

Many users navigate exclusively with a keyboard, using the Tab key to move between interactive elements. Native interactive elements (`<a>`, `<button>`, `<input>`) are focusable automatically; `tabindex` lets you adjust focus behavior for other elements.

```html
<div tabindex="0">Focusable, in natural tab order</div>
<div tabindex="-1">Focusable only via script, not by tabbing</div>
```

Avoid positive `tabindex` values (like `tabindex="3"`) — they override the natural, predictable tab order and tend to create a confusing navigation experience.

---

## 39.2 Focus Styles

Browsers show a visible **focus indicator** (often an outline) around the currently focused element by default. Never remove it without replacing it with something equally visible.

```html
<style>
  /* Avoid: removes focus visibility entirely */
  button:focus { outline: none; }

  /* Prefer: replace with a clear, custom style */
  button:focus-visible {
    outline: 2px solid #1a73e8;
    outline-offset: 2px;
  }
</style>
```

Without a visible focus indicator, keyboard users lose track of where they are on the page entirely.

---

## 39.3 Skip Links

A **skip link** lets keyboard users jump straight past repeated navigation to the main content, instead of tabbing through every menu item on every page load.

```html
<a href="#main-content" class="skip-link">Skip to main content</a>
...
<nav>...</nav>
<main id="main-content">
  ...
</main>
```

Skip links are usually visually hidden until they receive keyboard focus, at which point they become visible — a small addition that meaningfully speeds up navigation for keyboard and screen reader users on any page with a large menu.

[Previous](./[38]-ARIA-Roles-States-and-Properties.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[40]-Accessible-Forms-Images-and-Media.md)
