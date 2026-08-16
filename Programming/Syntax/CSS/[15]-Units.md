[Previous](./[14]-Color-Formats.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[16]-Typography-and-Web-Fonts.md)

*Colors, Units & Typography*

# Lesson 15 - Units: Absolute Vs. Relative (px, em, rem, %, vw, vh)

## 15.1 Absolute Units: `px`

Pixels are a fixed, absolute unit — `1px` always renders as the same physical size regardless of any parent or context. They're predictable but don't scale with user font-size preferences or surrounding context.

```css
.box {
  width: 300px;
  font-size: 16px;
}
```

---

## 15.2 Relative Unit: `em`

`em` is relative to the **font-size of the current element** (or its parent, if the current element doesn't set its own). This makes `em` compound — nested elements using `em` multiply against each other, which can cause unexpected scaling.

```css
.parent { font-size: 20px; }
.child  { font-size: 1.5em; } /* 20px * 1.5 = 30px */
.grandchild { font-size: 1.5em; } /* 30px * 1.5 = 45px, not 30px! */
```

`em` is most predictable for properties like `padding` or `margin` on a *single* element, where you want spacing to scale directly with that element's own font size.

---

## 15.3 Relative Unit: `rem`

`rem` ("root em") is always relative to the **root element's** (`<html>`) font-size, regardless of nesting. This avoids the compounding problem of `em` and makes `rem` the standard choice for most sizing in modern CSS.

```css
html { font-size: 16px; } /* default in most browsers */

h1 { font-size: 2rem; }    /* 32px, always, no matter where it's nested */
p  { font-size: 1rem; }    /* 16px */
```

Because `rem` scales from the root font-size, if a user increases their browser's default font size for accessibility, everything sized in `rem` scales proportionally.

---

## 15.4 Percentage: `%`

Percentages are relative to a value from the parent — which reference value depends on the property. `width`/`height` percentages are relative to the parent's size; `font-size` percentages are relative to the parent's font-size.

```css
.parent { width: 400px; }
.child  { width: 50%; }   /* 200px */
```

---

## 15.5 Viewport Units: `vw` And `vh`

`vw` and `vh` are relative to the browser's viewport (visible window) size: `1vw` = 1% of viewport width, `1vh` = 1% of viewport height. These are useful for elements that should scale with the screen itself, like full-height hero sections.

```css
.hero {
  height: 100vh;   /* full viewport height */
  width: 100vw;    /* full viewport width */
}

h1 {
  font-size: 5vw;  /* scales with screen width — used carefully, for fluid type */
}
```

---

## 15.6 Choosing The Right Unit

| Unit | Relative To | Common Use |
|---|---|---|
| `px` | Nothing (fixed) | Borders, fine details |
| `em` | Current element's font-size | Component-scoped spacing |
| `rem` | Root font-size | Font sizes, most spacing (recommended default) |
| `%` | Parent's size | Fluid widths |
| `vw`/`vh` | Viewport size | Full-screen sections, fluid type |

[Previous](./[14]-Color-Formats.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[16]-Typography-and-Web-Fonts.md)
