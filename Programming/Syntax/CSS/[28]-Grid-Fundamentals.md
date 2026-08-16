[Previous](./[27]-Common-Flexbox-Patterns.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[29]-Grid-Rows-Columns-and-Template-Areas.md)

*CSS Grid*

# Lesson 28 - Grid Fundamentals (Grid Container & Items)

## 28.1 Creating A Grid Container

Setting `display: grid` on an element turns it into a **grid container**, and its direct children become **grid items** — positioned into a two-dimensional grid of rows and columns.

```css
.container {
  display: grid;
}
```

Without any further configuration, a basic grid still just stacks items into a single column — the real power of Grid comes from explicitly defining rows and columns, covered in Lesson 29.

---

## 28.2 One-Dimensional Vs. Two-Dimensional

Flexbox (Lessons 24–27) arranges items along a single axis at a time. Grid arranges items along **two axes simultaneously** — rows and columns together — making it the better tool for full page layouts and any design that needs precise alignment in both directions at once.

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: auto 1fr auto;
}
```

This single declaration lays out a full 3-column, 3-row grid — something that would require nested Flexbox containers to replicate.

---

## 28.3 The Grid Line Concept

Grid layout is built around numbered **grid lines** — the boundaries between rows and columns, starting at `1`. A 3-column grid has 4 vertical grid lines (before column 1, between 1–2, between 2–3, and after column 3).

```
  1    2    3    4
  |    |    |    |
  | col1 | col2 | col3 |
```

Grid items can be placed by referencing these line numbers directly, which is covered in depth in Lesson 30.

---

## 28.4 Container Vs. Item Properties

Like Flexbox, Grid properties split into container-level and item-level groups:

- **Container properties**: `grid-template-columns`, `grid-template-rows`, `grid-template-areas`, `gap` (Lesson 29)
- **Item properties**: `grid-column`, `grid-row`, `justify-self`, `align-self` (Lesson 30)

```css
/* Container property */
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
}

/* Item property */
.item {
  grid-column: 1 / 3; /* spans from line 1 to line 3 */
}
```

---

## 28.5 `gap`

Just like in Flexbox, `gap` adds consistent spacing between grid rows and columns, without any extra markup.

```css
.container {
  display: grid;
  gap: 16px;        /* row and column gap */
  gap: 16px 24px;    /* row-gap column-gap */
}
```

[Previous](./[27]-Common-Flexbox-Patterns.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[29]-Grid-Rows-Columns-and-Template-Areas.md)
