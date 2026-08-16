[Previous](./[23]-Z-index-and-Stacking-Contexts.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[25]-Flex-Direction-Wrap-and-Alignment.md)

*Flexbox*

# Lesson 24 - Flexbox Fundamentals (Flex Container & Items)

## 24.1 Creating A Flex Container

Setting `display: flex` on an element turns it into a **flex container**, and its direct children automatically become **flex items** — laid out along a single row or column, without floats or manual positioning.

```css
.container {
  display: flex;
}
```

```html
<div class="container">
  <div>Item 1</div>
  <div>Item 2</div>
  <div>Item 3</div>
</div>
```

By default, the three items sit side by side in a row, each only as wide as its content.

---

## 24.2 The Two Axes

Flexbox layout is built around two axes:

- The **main axis** — the primary direction items are laid out (horizontal, by default)
- The **cross axis** — perpendicular to the main axis (vertical, by default)

Most Flexbox properties are described in relation to these axes rather than fixed directions like "left" or "top," which is what makes Flexbox so effective for both horizontal and vertical layouts using the same set of properties.

---

## 24.3 Container Vs. Item Properties

Flexbox properties split into two groups:

- **Container properties** — applied to the parent with `display: flex`, controlling overall layout behavior (`flex-direction`, `justify-content`, `align-items`, `flex-wrap` — Lesson 25)
- **Item properties** — applied to individual children, controlling how each one grows, shrinks, or sizes itself (`flex-grow`, `flex-shrink`, `flex-basis` — Lesson 26)

```css
/* Container property */
.container {
  display: flex;
  justify-content: center;
}

/* Item property */
.item {
  flex-grow: 1;
}
```

---

## 24.4 Why Flexbox?

Before Flexbox, centering an element vertically or building equal-height columns required awkward workarounds (Lesson 22). Flexbox solves these problems directly and predictably:

```css
/* Perfectly centering content, both horizontally and vertically */
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

Flexbox excels at **one-dimensional** layouts — arranging items in a single row or column. For two-dimensional layouts (rows *and* columns together), Grid (Lessons 28–32) is generally the better tool, as covered in Lesson 32.

[Previous](./[23]-Z-index-and-Stacking-Contexts.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[25]-Flex-Direction-Wrap-and-Alignment.md)
