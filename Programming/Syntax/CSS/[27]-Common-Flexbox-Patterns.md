[Previous](./[26]-Flex-Grow-Shrink-and-Basis.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[28]-Grid-Fundamentals.md)

*Flexbox*

# Lesson 27 - Common Flexbox Layout Patterns

This lesson brings together the properties from Lessons 24–26 into ready-to-use, real-world patterns.

## 27.1 Perfect Centering

The single most common use of Flexbox — centering content both horizontally and vertically.

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

---

## 27.2 Navigation Bar

A header with a logo on the left and navigation links on the right.

```css
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 16px 24px;
}

.nav-links {
  display: flex;
  gap: 24px;
}
```

---

## 27.3 Sticky Footer

A footer that stays at the bottom of the viewport even on short pages, without extra scripting.

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

main {
  flex: 1; /* pushes footer down by consuming all leftover vertical space */
}
```

---

## 27.4 Equal-Height Cards

Flex items automatically stretch to match the tallest sibling by default (`align-items: stretch`), making equal-height cards trivial.

```css
.card-row {
  display: flex;
  gap: 16px;
}

.card {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.card-footer {
  margin-top: auto; /* pushes footer content to the bottom of each card */
}
```

---

## 27.5 Responsive Wrapping Grid Of Cards

A simple, responsive card layout using `flex-wrap` — items reflow automatically as space changes.

```css
.card-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 16px;
}

.card {
  flex: 1 1 250px; /* grow, shrink, minimum ~250px before wrapping */
}
```

---

## 27.6 Media Object (Image + Text Side By Side)

A classic pattern: a fixed-size image next to flexible text content.

```css
.media {
  display: flex;
  gap: 16px;
  align-items: flex-start;
}

.media-image {
  flex: 0 0 64px; /* fixed size, never grows or shrinks */
}

.media-body {
  flex: 1; /* fills remaining space */
}
```

[Previous](./[26]-Flex-Grow-Shrink-and-Basis.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[28]-Grid-Fundamentals.md)
