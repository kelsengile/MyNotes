[Previous](./[10]-The-Box-Model.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[12]-Margin-Collapsing.md)

*The Box Model*

# Lesson 11 - Box Sizing (`box-sizing: border-box`)

## 11.1 The Default: `content-box`

By default, the `box-sizing` property is set to `content-box`. This means `width` and `height` only size the content area — padding and border are added on top, increasing the total rendered size.

```css
.box {
  box-sizing: content-box; /* default */
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Rendered width = 200 + 40 (padding) + 10 (border) = 250px */
}
```

This makes it hard to predict an element's actual footprint, especially when padding or border values change later.

---

## 11.2 The Alternative: `border-box`

Setting `box-sizing: border-box` changes what `width` and `height` measure — they now include padding and border, so the element's total rendered size matches the value you set.

```css
.box {
  box-sizing: border-box;
  width: 200px;
  padding: 20px;
  border: 5px solid black;
  /* Rendered width = 200px, exactly */
}
```

The content area simply shrinks to make room for padding and border, keeping the total size predictable and consistent.

---

## 11.3 The Universal Border-Box Reset

Because `border-box` is far more intuitive for layout, most modern projects apply it globally at the very start of their stylesheet.

```css
*, *::before, *::after {
  box-sizing: border-box;
}
```

Including `::before` and `::after` ensures generated content (Lesson 9) follows the same sizing rules as everything else.

---

## 11.4 Why It Matters For Layout

`border-box` becomes especially important once you start building layouts with Flexbox and Grid (Lessons 24–32), where precise, predictable sizing across many elements is essential. Without it, adding padding or a border to one element in a row can silently break alignment with its siblings.

```css
/* Without border-box, these two boxes render different total widths
   despite both stating width: 50% */
.column {
  width: 50%;
  padding: 16px; /* pushes actual rendered width past 50% */
}
```

[Previous](./[10]-The-Box-Model.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[12]-Margin-Collapsing.md)
