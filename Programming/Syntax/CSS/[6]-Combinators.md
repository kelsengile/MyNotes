[Previous](./[5]-Basic-Selectors.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[7]-Attribute-Selectors.md)

*Selectors*

# Lesson 6 - Combinators

Combinators let you select elements based on their relationship to other elements in the document.

## 6.1 Descendant Combinator (space)

Selects any element that is nested anywhere inside another element, no matter how deep.

```html
<article>
  <p>Direct child paragraph</p>
  <div><p>Nested paragraph</p></div>
</article>
```

```css
article p {
  color: darkslategray;
}
```

Both paragraphs above are styled, since both are descendants of `<article>`, regardless of nesting depth.

---

## 6.2 Child Combinator (`>`)

Selects only elements that are **direct** children of another element — not deeper descendants.

```css
article > p {
  color: darkslategray;
}
```

With the same HTML as above, only the first, directly-nested `<p>` is styled — the one wrapped in a `<div>` is skipped because it's a grandchild, not a direct child.

---

## 6.3 Adjacent Sibling Combinator (`+`)

Selects an element that comes immediately after another specific element, sharing the same parent.

```html
<h2>Title</h2>
<p>This paragraph is styled.</p>
<p>This one is not.</p>
```

```css
h2 + p {
  font-weight: bold;
}
```

Only the paragraph directly following the `<h2>` is matched — the second `<p>` is not adjacent to the `<h2>`, so it's skipped.

---

## 6.4 General Sibling Combinator (`~`)

Selects all elements that share the same parent and come after a specific element — not just the immediate next one.

```css
h2 ~ p {
  color: gray;
}
```

With the same HTML as 6.3, **both** paragraphs are matched, since both come after the `<h2>` and share its parent.

---

## 6.5 Combining Combinators

Combinators can be chained to express precise structural relationships.

```css
nav > ul > li + li {
  border-left: 1px solid #ccc;
}
```

This targets every `<li>` that directly follows another `<li>`, inside a `<ul>` that is a direct child of `<nav>` — a common pattern for adding dividers between navigation items.

[Previous](./[5]-Basic-Selectors.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[7]-Attribute-Selectors.md)
