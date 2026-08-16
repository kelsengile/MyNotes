[Previous](./[10]-Special-Characters-and-Entities.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[12]-Semantic-Page-Structure.md)

*Semantic HTML*

# Lesson 11 - Why Semantics Matter

## 11.1 What Is Semantic HTML?

**Semantic HTML** means choosing elements based on the *meaning* of the content, not just its appearance. A `<button>` behaves and communicates differently than a `<div>` styled to look like a button, even if they're visually identical.

```html
<!-- Non-semantic -->
<div class="button" onclick="submitForm()">Submit</div>

<!-- Semantic -->
<button type="submit">Submit</button>
```

The `<button>` version is focusable with the keyboard, works with Enter/Space out of the box, and is automatically announced as a button by screen readers — none of which the `<div>` gets for free.

---

## 11.2 Benefits for Accessibility

Screen readers and other assistive technology rely on semantic meaning to describe a page to users who can't see it. A page built from meaningful elements (`<nav>`, `<button>`, `<h1>`) is automatically navigable; a page built entirely from unstyled `<div>`s is not, regardless of how it looks visually.

---

## 11.3 Benefits for SEO and Maintainability

- **Search engines** use semantic structure (headings, `<article>`, `<nav>`) to understand what a page is about and how it's organized, which affects search rankings.
- **Other developers** (including future you) can read semantic markup far more easily than a wall of generic `<div>`s — the tag names themselves document the page's structure.

Throughout the rest of this section, you'll learn the specific semantic elements HTML provides for structuring a page — and when to reach for them instead of a generic container.

[Previous](./[10]-Special-Characters-and-Entities.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[12]-Semantic-Page-Structure.md)
