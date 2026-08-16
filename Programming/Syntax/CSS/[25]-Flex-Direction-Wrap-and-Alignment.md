[Previous](./[24]-Flexbox-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[26]-Flex-Grow-Shrink-and-Basis.md)

*Flexbox*

# Lesson 25 - Flex Direction, Wrap And Alignment

## 25.1 `flex-direction`

Sets the main axis direction (Lesson 24), determining which way items flow.

```css
.row { flex-direction: row; }              /* default: left to right */
.row-reverse { flex-direction: row-reverse; }  /* right to left */
.column { flex-direction: column; }        /* top to bottom */
.column-reverse { flex-direction: column-reverse; } /* bottom to top */
```

Switching to `column` swaps the main and cross axes — this matters because `justify-content` and `align-items` (below) always refer to the main and cross axis, not fixed directions.

---

## 25.2 `flex-wrap`

By default, flex items try to fit on a single line, shrinking to do so if needed. `flex-wrap` lets items move onto new lines instead.

```css
.container {
  display: flex;
  flex-wrap: wrap;       /* items wrap onto new lines as needed */
  flex-wrap: nowrap;     /* default — everything stays on one line */
  flex-wrap: wrap-reverse; /* wraps, but new lines stack in reverse */
}
```

`flex-direction` and `flex-wrap` are often combined using the `flex-flow` shorthand:

```css
.container {
  flex-flow: row wrap;
}
```

---

## 25.3 `justify-content` (Main Axis Alignment)

Aligns items along the **main axis**.

```css
.container {
  display: flex;
  justify-content: flex-start;    /* default — items packed at the start */
  justify-content: flex-end;      /* packed at the end */
  justify-content: center;        /* packed in the center */
  justify-content: space-between; /* even space between items, none at the edges */
  justify-content: space-around;  /* even space around each item */
  justify-content: space-evenly;  /* perfectly equal space everywhere */
}
```

---

## 25.4 `align-items` (Cross Axis Alignment)

Aligns items along the **cross axis**.

```css
.container {
  display: flex;
  align-items: stretch;     /* default — items stretch to fill cross axis */
  align-items: flex-start;  /* aligned to the start of the cross axis */
  align-items: flex-end;    /* aligned to the end */
  align-items: center;      /* centered on the cross axis */
  align-items: baseline;    /* aligned by text baseline */
}
```

---

## 25.5 `align-content` (Multi-Line Alignment)

When `flex-wrap: wrap` creates multiple lines, `align-content` controls how those lines are distributed along the cross axis as a group — distinct from `align-items`, which aligns items *within* a single line.

```css
.container {
  display: flex;
  flex-wrap: wrap;
  align-content: space-between; /* distributes the wrapped lines themselves */
}
```

---

## 25.6 `gap`

Adds consistent spacing between flex items, without needing margin on individual items (and without the extra space appearing at the outer edges that margin would cause).

```css
.container {
  display: flex;
  gap: 16px;          /* row and column gap, same value */
  gap: 16px 8px;       /* row-gap column-gap */
}
```

[Previous](./[24]-Flexbox-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[26]-Flex-Grow-Shrink-and-Basis.md)
