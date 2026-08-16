[Previous](./[28]-Grid-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[30]-Grid-Alignment-and-Placement.md)

*CSS Grid*

# Lesson 29 - Defining Rows, Columns And Grid Template Areas

## 29.1 `grid-template-columns` And `grid-template-rows`

Explicitly define the size of each row/column, space-separated. Any valid length unit works, including the special `fr` unit (see below).

```css
.container {
  display: grid;
  grid-template-columns: 200px 200px 200px; /* three fixed 200px columns */
  grid-template-rows: 100px 100px;          /* two fixed 100px rows */
}
```

---

## 29.2 The `fr` Unit

`fr` ("fraction") represents a share of the available leftover space in the grid container — similar in spirit to `flex-grow`, but for Grid.

```css
.container {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr; /* three equal-width columns */
  grid-template-columns: 2fr 1fr;     /* first column twice as wide as the second */
  grid-template-columns: 250px 1fr;   /* fixed sidebar + fluid main content */
}
```

`fr` and fixed units can be freely mixed — fixed-size tracks are sized first, then remaining space is divided among the `fr` tracks.

---

## 29.3 The `repeat()` Function

`repeat()` avoids writing the same value over and over for grids with many equal tracks.

```css
.container {
  grid-template-columns: repeat(3, 1fr);       /* same as: 1fr 1fr 1fr */
  grid-template-columns: repeat(4, 100px);      /* four 100px columns */
  grid-template-columns: repeat(2, 1fr 2fr);    /* repeats the whole pattern: 1fr 2fr 1fr 2fr */
}
```

---

## 29.4 `grid-template-areas`

Names regions of the grid as ASCII-art-like strings, then assigns items to those named regions with `grid-area` — an especially readable way to define page layouts.

```css
.container {
  display: grid;
  grid-template-columns: 200px 1fr;
  grid-template-rows: auto 1fr auto;
  grid-template-areas:
    "sidebar header"
    "sidebar main"
    "sidebar footer";
}

.header { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

Each quoted string represents one row, and each word within it represents one column's content in that row — repeating a name across cells makes an item span that many cells (as `sidebar` does across all three rows above).

---

## 29.5 Implicit Rows And Columns

If more items exist than the explicit grid defines room for, the grid automatically creates additional ("implicit") tracks. `grid-auto-rows` and `grid-auto-columns` control the sizing of those implicit tracks.

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: 150px; /* any extra rows created automatically will be 150px tall */
}
```

[Previous](./[28]-Grid-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[30]-Grid-Alignment-and-Placement.md)
