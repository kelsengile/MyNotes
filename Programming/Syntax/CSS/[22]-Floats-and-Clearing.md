[Previous](./[21]-Positioning.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[23]-Z-index-and-Stacking-Contexts.md)

*Layout Fundamentals*

# Lesson 22 - Floats And Clearing (Legacy Layout)

## 22.1 What `float` Does

`float` was originally designed to let text wrap around an image, similar to a newspaper layout. Setting `float` removes an element from normal flow horizontally, pushing it to one side while allowing inline content to wrap around it.

```css
img {
  float: left;
  margin-right: 16px;
}
```

```html
<img src="photo.jpg">
<p>This paragraph text will wrap around the floated image, flowing along its right side.</p>
```

---

## 22.2 Floats For Layout (Historical Context)

Before Flexbox and Grid existed, developers floated multiple block-level elements side by side to build column layouts.

```css
.column {
  float: left;
  width: 33.33%;
}
```

```html
<div class="column">One</div>
<div class="column">Two</div>
<div class="column">Three</div>
```

This technique is now considered **legacy** — Flexbox (Lessons 24–27) and Grid (Lessons 28–32) solve the same problems far more reliably and predictably, and should be used for layout in modern projects.

---

## 22.3 The Collapsing Parent Problem

A parent element that contains only floated children doesn't "see" their height — since floated elements are out of normal flow, the parent collapses to zero height unless something forces it to account for its floated content.

```html
<div class="container">
  <div class="column">One</div>
  <div class="column">Two</div>
</div>
```

```css
.container {
  /* Without a fix, this element has 0 height, even though its children don't */
}
```

---

## 22.4 The `clear` Property

`clear` forces an element to move below any preceding floated elements, rather than wrapping alongside them.

```css
.clearfix-element {
  clear: both; /* clears floats on both left and right */
}
```

---

## 22.5 The Clearfix Hack

A common workaround for the collapsing parent problem uses a `::after` pseudo-element (Lesson 9) to force the parent to account for its floated children.

```css
.container::after {
  content: "";
  display: table;
  clear: both;
}
```

---

## 22.6 Modern Alternative

In modern CSS, simply giving the parent a new formatting context avoids the collapsing problem entirely, without needing floats or the clearfix hack at all:

```css
.container {
  display: flow-root; /* modern, purpose-built fix for the same problem */
}
```

Floats remain useful for their original purpose — wrapping text around an image — but for actual page layout, prefer Flexbox or Grid.

[Previous](./[21]-Positioning.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[23]-Z-index-and-Stacking-Contexts.md)
