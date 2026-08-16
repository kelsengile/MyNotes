[Previous](./[15]-Time-Address-and-Other-Semantic-Elements.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[17]-Responsive-Images.md)

*Media*

# Lesson 16 - Images

## 16.1 The `<img>` Element

`<img>` embeds an image. It's a void element (no closing tag) and requires a `src` attribute pointing to the image file.

```html
<img src="photo.jpg" alt="A sunset over the ocean">
```

You can also set `width` and `height` attributes — this reserves space for the image before it loads, preventing the page from jumping around as images load in.

```html
<img src="photo.jpg" alt="A sunset over the ocean" width="800" height="450">
```

---

## 16.2 Alt Text

The `alt` attribute provides a text alternative for the image — read aloud by screen readers, shown if the image fails to load, and used by search engines.

```html
<img src="cat.jpg" alt="An orange tabby cat sleeping on a windowsill">
```

Guidelines for good alt text:

- Describe the image's *content and purpose*, not just its filename.
- Keep it concise — a sentence or less in most cases.
- If an image is purely decorative and adds no information, use an empty `alt=""` so screen readers skip it entirely.

```html
<img src="divider-swirl.png" alt="">
```

---

## 16.3 Image Formats

| Format | Best for |
|---|---|
| **JPEG** | Photos, gradients — lossy compression, smaller files |
| **PNG** | Graphics needing transparency, sharp edges |
| **SVG** | Logos, icons — scales infinitely without quality loss |
| **WebP** | Modern format, smaller than JPEG/PNG at similar quality |
| **GIF** | Simple looping animations |

Choosing the right format keeps pages fast-loading — a photo saved as PNG is often needlessly large, while a logo saved as JPEG can look blurry.

[Previous](./[15]-Time-Address-and-Other-Semantic-Elements.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[17]-Responsive-Images.md)
