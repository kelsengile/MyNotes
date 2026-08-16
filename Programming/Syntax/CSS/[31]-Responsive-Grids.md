[Previous](./[30]-Grid-Alignment-and-Placement.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[32]-Grid-vs-Flexbox.md)

*CSS Grid*

# Lesson 31 - Responsive Grids With `auto-fit`, `auto-fill` And `minmax()`

## 31.1 `minmax()`

`minmax(min, max)` gives a track a size range instead of a fixed value — the browser picks a size within those bounds depending on available space.

```css
.container {
  display: grid;
  grid-template-columns: minmax(150px, 1fr) minmax(150px, 1fr);
}
```

Each column here is never smaller than `150px`, but grows to fill available space equally beyond that, up to `1fr` each.

---

## 31.2 `auto-fill` Vs. `auto-fit`

Both let the number of columns adjust automatically based on available space, combined with `repeat()` and `minmax()` — this is the standard pattern for a fully responsive grid with no media queries at all.

```css
.container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
}
```

This creates as many `200px`-minimum columns as will fit in the container, each growing to share any remaining space equally.

- **`auto-fill`** keeps empty tracks in place if there isn't enough content to fill a row, preserving their width (useful when track size matters even with few items).
- **`auto-fit`** collapses those empty tracks to zero width, letting existing items stretch to fill the freed-up space instead.

```css
/* With only 2 items in a container that fits 4 columns: */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); /* 2 items + 2 empty, collapsed-width tracks */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));  /* 2 items stretch to fill the full row */
```

---

## 31.3 A Complete Responsive Card Grid

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 24px;
}
```

This single rule automatically reflows from a multi-column desktop layout down to a single column on narrow screens, entirely without media queries (which are covered formally in Lesson 34).

---

## 31.4 Combining With Media Queries

`auto-fit`/`auto-fill` handle most responsive needs on their own, but media queries are still useful for adjusting things `minmax()` can't reach, like `gap` or unrelated layout changes.

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 12px;
}

@media (min-width: 768px) {
  .card-grid {
    gap: 24px;
  }
}
```

---

## 31.5 When To Use `minmax()` Without `repeat()`

`minmax()` is also useful outside of `repeat()`, for individually sized tracks that still need flexible bounds.

```css
.container {
  display: grid;
  grid-template-columns: minmax(200px, 300px) 1fr;
}
```

Here the first column flexes between `200px` and `300px` depending on space, while the second absorbs whatever remains.

[Previous](./[30]-Grid-Alignment-and-Placement.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[32]-Grid-vs-Flexbox.md)
