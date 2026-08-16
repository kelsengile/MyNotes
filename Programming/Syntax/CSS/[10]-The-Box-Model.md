[Previous](./[9]-Pseudo-Elements.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[11]-Box-Sizing.md)

*The Box Model*

# Lesson 10 - The Box Model: Content, Padding, Border And Margin

## 10.1 Every Element Is A Box

In CSS, every single element on a page is rendered as a rectangular box, made of four layers, from the inside out:

1. **Content** — the actual text, image, or nested elements
2. **Padding** — space between the content and the border
3. **Border** — a line that wraps around the padding
4. **Margin** — space outside the border, separating the element from its neighbors

```
┌───────────────── margin ─────────────────┐
│  ┌───────────── border ─────────────┐    │
│  │  ┌─────────── padding ───────┐   │    │
│  │  │        content            │   │    │
│  │  └────────────────────────────┘   │    │
│  └────────────────────────────────────┘    │
└───────────────────────────────────────────┘
```

---

## 10.2 Content

The content box holds the element's actual content and is sized with `width` and `height`.

```css
.box {
  width: 200px;
  height: 100px;
}
```

---

## 10.3 Padding

Padding adds space *inside* the border, pushing the content inward. It can be set for all sides at once or per side.

```css
.box {
  padding: 20px;                    /* all sides */
  padding: 10px 20px;               /* top/bottom, left/right */
  padding: 10px 20px 15px 5px;      /* top, right, bottom, left */
  padding-top: 10px;                /* individual side */
}
```

Padding takes on the element's background color, so it's useful for creating breathing room around content without needing an extra element.

---

## 10.4 Border

A border sits between the padding and margin, and needs three things to be visible: width, style, and color.

```css
.box {
  border-width: 2px;
  border-style: solid;
  border-color: #333;

  /* shorthand */
  border: 2px solid #333;

  /* individual sides */
  border-bottom: 1px dashed gray;
}
```

Common `border-style` values include `solid`, `dashed`, `dotted`, `double`, and `none`.

---

## 10.5 Margin

Margin sits outside the border and creates space between an element and its neighbors. It behaves just like padding syntactically.

```css
.box {
  margin: 20px;
  margin: 10px auto;   /* common trick to horizontally center a block element */
}
```

Unlike padding, margin is transparent — it never shows a background color, and adjacent vertical margins can *collapse* into each other, which is covered in Lesson 12.

---

## 10.6 Total Element Size

By default, `width` and `height` apply only to the content box — padding and border are added *on top* of that, which can make total element size harder to predict. Lesson 11 covers `box-sizing`, which changes this behavior.

```css
.box {
  width: 200px;
  padding: 20px;
  border: 2px solid black;
  /* Actual rendered width: 200 + 20+20 (padding) + 2+2 (border) = 244px */
}
```

[Previous](./[9]-Pseudo-Elements.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[11]-Box-Sizing.md)
