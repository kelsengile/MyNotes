[Previous](./[12]-Margin-Collapsing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[14]-Color-Formats.md)

*The Box Model*

# Lesson 13 - Display Property

The `display` property determines how an element generates boxes and participates in layout — it's one of the most fundamental properties in CSS.

## 13.1 `block`

Block-level elements start on a new line and stretch to fill the full width of their parent by default. They respect `width`, `height`, and all margin/padding values. Examples include `<div>`, `<p>`, and `<h1>`–`<h6>`.

```css
div {
  display: block; /* default for <div> */
}
```

---

## 13.2 `inline`

Inline elements flow within the surrounding text, only take up as much width as their content needs, and sit side-by-side without forcing a line break. Examples include `<span>`, `<a>`, and `<strong>`.

```css
span {
  display: inline; /* default for <span> */
}
```

Inline elements **ignore** `width` and `height`, and only respect horizontal (`left`/`right`) margin and padding — vertical margin doesn't push surrounding lines apart.

---

## 13.3 `inline-block`

A hybrid: the element flows inline with surrounding content (like `inline`), but respects `width`, `height`, and all margin/padding (like `block`).

```css
.badge {
  display: inline-block;
  width: 80px;
  padding: 4px 8px;
}
```

This was a common technique for building simple horizontal layouts before Flexbox and Grid became widely supported.

---

## 13.4 `none`

Removes the element from the page entirely — it takes up no space, as if it were never in the document at all.

```css
.hidden {
  display: none;
}
```

This differs from `visibility: hidden`, which hides an element visually but still reserves its space in the layout.

---

## 13.5 `contents`

Makes the element itself disappear from the box model, while its children behave as if they were direct children of its parent. The element still exists in the DOM (and JavaScript can still find it), but it generates no box of its own.

```css
.wrapper {
  display: contents;
}
```

This is useful when a wrapper element exists only for markup/semantic reasons but is getting in the way of a layout method like Grid, which expects direct children.

---

## 13.6 Layout-Defining Values

`display` also introduces entirely new layout systems for an element's children:

```css
.flex-container { display: flex; }  /* Lesson 24 */
.grid-container  { display: grid; } /* Lesson 28 */
```

These are covered in depth in the Flexbox and CSS Grid sections of this course.

[Previous](./[12]-Margin-Collapsing.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[14]-Color-Formats.md)
