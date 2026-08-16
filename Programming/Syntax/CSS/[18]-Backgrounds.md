[Previous](./[17]-Text-Styling.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[19]-Borders-Shadows-and-Outlines.md)

*Colors, Units & Typography*

# Lesson 18 - Backgrounds (Color, Image, Gradient, Position, Size, Repeat)

## 18.1 `background-color`

The simplest background property — fills the element's padding and content box with a solid color.

```css
.card {
  background-color: #f5f5f5;
}
```

---

## 18.2 `background-image`

Sets an image as the element's background, layered above `background-color`.

```css
.hero {
  background-color: #222; /* fallback while image loads, or if it fails */
  background-image: url("/images/hero.jpg");
}
```

Multiple images can be layered, comma-separated, with the first listed appearing on top.

```css
.card {
  background-image: url("/images/overlay.png"), url("/images/photo.jpg");
}
```

---

## 18.3 `background-repeat`

Controls whether/how a background image tiles.

```css
.pattern { background-repeat: repeat; }      /* default — tiles both directions */
.no-repeat { background-repeat: no-repeat; }
.repeat-x { background-repeat: repeat-x; }    /* tile horizontally only */
```

---

## 18.4 `background-position`

Positions the image within the element's box, using keywords, percentages, or lengths.

```css
.hero {
  background-position: center center;
  background-position: top right;
  background-position: 20px 50%;
}
```

---

## 18.5 `background-size`

Controls how the image is scaled to fit its container.

```css
.cover  { background-size: cover; }   /* fills the box, cropping if needed */
.contain { background-size: contain; } /* fits entirely inside, may leave gaps */
.fixed-size { background-size: 200px 100px; }
```

`cover` is the most common choice for full-bleed hero images, since it always fills the container without distortion.

---

## 18.6 The `background` Shorthand

Combines all background properties in one declaration.

```css
.hero {
  background: url("/images/hero.jpg") center/cover no-repeat #222;
  /*           image                  position/size  repeat   fallback color */
}
```

---

## 18.7 Gradients

Gradients are generated as `background-image` values, using the `linear-gradient()`, `radial-gradient()`, or `conic-gradient()` functions — no image file required.

```css
.linear {
  background: linear-gradient(to right, tomato, gold);
}

.radial {
  background: radial-gradient(circle, white, lightblue);
}

.angled {
  background: linear-gradient(45deg, #ff6347, #4682b4);
}

.multi-stop {
  background: linear-gradient(to bottom, red 0%, yellow 50%, green 100%);
}
```

Gradients can also be layered with images: `background: linear-gradient(rgba(0,0,0,0.4), rgba(0,0,0,0.4)), url("photo.jpg");` is a common way to darken a background photo for text legibility.

[Previous](./[17]-Text-Styling.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[19]-Borders-Shadows-and-Outlines.md)
