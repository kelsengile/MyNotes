[Previous](./[12]-Semantic-Page-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[14]-Figures-Captions-and-Blockquotes.md)

*Semantic HTML*

# Lesson 13 - Generic Containers: `div` vs. `span`

## 13.1 The `<div>` Element

`<div>` is a generic **block-level** container with no semantic meaning of its own. It's used purely for grouping content — usually for styling or scripting purposes — when no semantic element fits.

```html
<div class="card">
  <h3>Product Name</h3>
  <p>Product description.</p>
</div>
```

---

## 13.2 The `<span>` Element

`<span>` is a generic **inline** container — it doesn't force a line break, and it's used to wrap a small piece of text or another inline element, typically for styling.

```html
<p>The price is <span class="highlight">$19.99</span> today only.</p>
```

---

## 13.3 When to Use Generic vs. Semantic

The general rule: **reach for a semantic element first**, and only fall back to `<div>` or `<span>` when nothing else fits.

| Need | Use |
|---|---|
| Main navigation | `<nav>`, not `<div class="nav">` |
| A blog post | `<article>`, not `<div class="post">` |
| Grouping form controls for styling | `<div>` is fine — no semantic alternative |
| Wrapping a word to color it | `<span>` is fine — no semantic alternative |

`<div>` and `<span>` aren't "wrong" — they're the right tool whenever there's genuinely no meaning to convey, only grouping or styling. The mistake is reaching for them out of habit when a more descriptive element already exists.

[Previous](./[12]-Semantic-Page-Structure.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[14]-Figures-Captions-and-Blockquotes.md)
