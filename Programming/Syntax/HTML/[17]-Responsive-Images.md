[Previous](./[16]-Images.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[18]-Audio-and-Video.md)

*Media*

# Lesson 17 - Responsive Images

## 17.1 `srcset` and `sizes`

`srcset` lets you offer the browser several versions of the same image at different resolutions, so it can pick the most appropriate one for the user's screen.

```html
<img
  src="photo-800.jpg"
  srcset="photo-400.jpg 400w, photo-800.jpg 800w, photo-1200.jpg 1200w"
  sizes="(max-width: 600px) 100vw, 50vw"
  alt="A mountain landscape">
```

- Each entry in `srcset` lists a file and its actual pixel width (`400w` means 400 pixels wide).
- `sizes` tells the browser how much screen space the image will occupy at different viewport widths, so it can calculate which file to download.

---

## 17.2 The `<picture>` Element

`<picture>` gives you more control by letting you specify multiple `<source>` elements, each with its own conditions, with an `<img>` as the fallback.

```html
<picture>
  <source media="(min-width: 800px)" srcset="banner-wide.jpg">
  <source media="(max-width: 799px)" srcset="banner-narrow.jpg">
  <img src="banner-wide.jpg" alt="Store banner">
</picture>
```

`<picture>` can also serve different image *formats*, falling back gracefully for older browsers:

```html
<picture>
  <source srcset="photo.webp" type="image/webp">
  <img src="photo.jpg" alt="A city skyline">
</picture>
```

---

## 17.3 Art Direction vs. Resolution Switching

- **Resolution switching** (`srcset`/`sizes` on `<img>`) serves the *same image*, just at different sizes — used purely for performance.
- **Art direction** (`<picture>` with `<source>`) serves *different crops or compositions* depending on screen size — e.g. a tightly cropped portrait image on mobile versus a wide landscape shot on desktop.

Use `srcset` alone for simple performance gains, and reach for `<picture>` when the image itself genuinely needs to change based on context.

[Previous](./[16]-Images.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[18]-Audio-and-Video.md)
