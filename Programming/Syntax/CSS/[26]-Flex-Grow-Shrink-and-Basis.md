[Previous](./[25]-Flex-Direction-Wrap-and-Alignment.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[27]-Common-Flexbox-Patterns.md)

*Flexbox*

# Lesson 26 - Flex Grow, Shrink And Basis

These three item-level properties (Lesson 24) control how individual flex items resize relative to each other, and are almost always used together via the `flex` shorthand.

## 26.1 `flex-basis`

Sets an item's initial size along the main axis, before any growing or shrinking is applied. It behaves like `width` (in a row) or `height` (in a column), but takes priority over them when both are set.

```css
.item {
  flex-basis: 200px;   /* start at 200px before grow/shrink adjustments */
  flex-basis: auto;    /* default — use the item's own width/content size */
  flex-basis: 0;       /* ignore content size, distribute entirely via flex-grow */
}
```

---

## 26.2 `flex-grow`

Determines how much of the container's **leftover space** an item should absorb, relative to its siblings. A value of `0` (the default) means the item won't grow beyond its `flex-basis`.

```css
.item-a { flex-grow: 1; }
.item-b { flex-grow: 2; } /* grows twice as much as .item-a */
```

If the container has extra space after all items are laid out, `.item-b` claims twice as much of that leftover space as `.item-a`.

---

## 26.3 `flex-shrink`

Determines how much an item shrinks when there isn't enough space to fit everything at their full `flex-basis`. A value of `1` (the default) allows shrinking; `0` prevents it entirely.

```css
.item-fixed {
  flex-shrink: 0; /* never shrink below its flex-basis, even if the row overflows */
}
```

This is a common way to keep an icon or fixed-width sidebar from being squeezed as the container narrows.

---

## 26.4 The `flex` Shorthand

`flex-grow`, `flex-shrink`, and `flex-basis` are almost always combined using the `flex` shorthand: `flex: grow shrink basis`.

```css
.item {
  flex: 1 1 0;    /* grow, shrink, basis — equivalent to flex: 1 */
  flex: 1;        /* shorthand for flex: 1 1 0% — item grows/shrinks equally with siblings */
  flex: auto;     /* shorthand for flex: 1 1 auto — grows/shrinks from its content size */
  flex: none;     /* shorthand for flex: 0 0 auto — item is fully rigid */
}
```

---

## 26.5 Practical Pattern: Equal-Width Columns

```css
.container {
  display: flex;
  gap: 16px;
}

.column {
  flex: 1; /* all columns grow and shrink equally, ending up equal width */
}
```

## 26.6 Practical Pattern: Fixed Sidebar + Fluid Content

```css
.sidebar {
  flex: 0 0 250px; /* never grows or shrinks, always 250px */
}

.main-content {
  flex: 1; /* takes up all remaining space */
}
```

[Previous](./[25]-Flex-Direction-Wrap-and-Alignment.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[27]-Common-Flexbox-Patterns.md)
