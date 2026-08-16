[Previous](./[29]-Grid-Rows-Columns-and-Template-Areas.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[31]-Responsive-Grids.md)

*CSS Grid*

# Lesson 30 - Grid Alignment And Placement

## 30.1 Placing Items By Line Number

Instead of (or alongside) `grid-template-areas`, items can be placed explicitly using grid line numbers (introduced in Lesson 28) with `grid-column` and `grid-row`.

```css
.item {
  grid-column: 1 / 3;   /* starts at line 1, ends at line 3 (spans 2 columns) */
  grid-row: 2 / 4;      /* starts at line 2, ends at line 4 (spans 2 rows) */
}
```

The `span` keyword is often more convenient than tracking exact end lines, since it's relative rather than absolute:

```css
.item {
  grid-column: 1 / span 2; /* start at line 1, span 2 columns */
  grid-row: span 3;        /* span 3 rows from wherever it's auto-placed */
}
```

---

## 30.2 The `grid-column`/`grid-row` Shorthand

```css
.item {
  grid-column: 2 / 4; /* shorthand for grid-column-start: 2; grid-column-end: 4; */
}
```

---

## 30.3 `justify-items` And `align-items`

Control how *all* items align within their individual grid cell — horizontally (`justify-items`) and vertically (`align-items`).

```css
.container {
  display: grid;
  justify-items: center; /* horizontally center every item within its cell */
  align-items: center;   /* vertically center every item within its cell */
}
```

Values include `start`, `end`, `center`, and `stretch` (the default) — the same vocabulary used in Flexbox's `align-items`.

---

## 30.4 `justify-self` And `align-self`

The per-item equivalents of `justify-items`/`align-items`, letting a single item override the container-wide alignment.

```css
.special-item {
  justify-self: end;  /* only this item aligns to the right of its cell */
  align-self: start;  /* only this item aligns to the top of its cell */
}
```

---

## 30.5 `justify-content` And `align-content`

While `justify-items`/`align-items` position content *within* each cell, `justify-content`/`align-content` position the **grid as a whole** within the container — relevant when the grid's total size is smaller than its container.

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 100px);
  justify-content: center; /* centers the entire 300px-wide grid horizontally */
}
```

---

## 30.6 Overlapping Items

Because Grid items are placed by line coordinates rather than flow, it's possible (and sometimes intentional) for two items to overlap the same cells — useful for layered visual effects.

```css
.background { grid-area: 1 / 1 / 3 / 3; }
.foreground { grid-area: 1 / 1 / 3 / 3; z-index: 1; } /* Lesson 23 */
```

[Previous](./[29]-Grid-Rows-Columns-and-Template-Areas.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[31]-Responsive-Grids.md)
