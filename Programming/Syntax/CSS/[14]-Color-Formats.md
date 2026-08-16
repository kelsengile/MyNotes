[Previous](./[13]-Display-Property.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[15]-Units.md)

*Colors, Units & Typography*

# Lesson 14 - Color Formats

## 14.1 Named Colors

CSS supports around 150 predefined color names, useful for quick prototyping.

```css
h1 { color: tomato; }
.box { background-color: lightblue; }
```

Named colors are limited and imprecise for design systems, so most real projects use one of the formats below instead.

---

## 14.2 Hexadecimal

Hex colors define red, green, and blue as two-digit hexadecimal values, prefixed with `#`.

```css
.box {
  color: #ff6347;      /* 6-digit */
  background: #36c;    /* 3-digit shorthand — same as #3366cc */
}
```

A shorthand 3-digit hex is valid when each pair of digits repeats (e.g., `#336699` → `#369`).

---

## 14.3 RGB And RGBA

`rgb()` defines a color using red, green, and blue channels (0–255 each). `rgba()` adds an alpha (opacity) channel from `0` (transparent) to `1` (opaque). Modern CSS also allows the alpha channel directly inside `rgb()`.

```css
.box {
  color: rgb(255, 99, 71);
  background: rgba(0, 0, 0, 0.5);      /* 50% transparent black */
  border-color: rgb(0 0 0 / 50%);      /* modern space-separated syntax */
}
```

---

## 14.4 HSL And HSLA

`hsl()` defines a color using **H**ue (0–360°), **S**aturation (0–100%), and **L**ightness (0–100%). This format is often more intuitive for humans — adjusting lightness alone gives predictable lighter/darker variants of the same color.

```css
.box {
  color: hsl(9, 100%, 64%);
  background: hsla(9, 100%, 64%, 0.5);
}
```

```css
/* Same hue and saturation, varying only lightness */
.light  { background: hsl(210, 80%, 80%); }
.medium { background: hsl(210, 80%, 50%); }
.dark   { background: hsl(210, 80%, 20%); }
```

---

## 14.5 The `currentColor` Keyword

`currentColor` refers to the element's own computed `color` value, and can be reused anywhere else a color is expected — useful for keeping icons, borders, or shadows in sync with text color automatically.

```css
.icon {
  color: crimson;
  border: 2px solid currentColor; /* border matches the text color */
}
```

---

## 14.6 Choosing A Format

| Format | Best For |
|---|---|
| Named | Quick prototypes |
| Hex | Design specs, most common in production |
| RGB(A) | When you need transparency and think in RGB |
| HSL(A) | Generating color variations (lighter/darker/more saturated) programmatically |

[Previous](./[13]-Display-Property.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[15]-Units.md)
