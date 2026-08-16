[Previous](./[32]-The-Head-Element-in-Depth.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[34]-Open-Graph-and-Social-Meta-Tags.md)

*Metadata & SEO*

# Lesson 33 - Meta Tags

## 33.1 Character Encoding

The `charset` meta tag declares which character encoding the document uses. Nearly every modern page should use `UTF-8`, which supports virtually every character and symbol in use today.

```html
<meta charset="UTF-8">
```

This should be one of the very first tags inside `<head>`, since the browser needs it before it can correctly parse the rest of the document's text.

---

## 33.2 The Viewport Meta Tag

The `viewport` meta tag controls how a page scales on mobile devices. Without it, mobile browsers render the page at a fixed desktop width and shrink it down, making text tiny.

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0">
```

- `width=device-width` matches the page's width to the device's screen width.
- `initial-scale=1.0` sets the initial zoom level to 100%.

This tag is essential for any responsive page.

---

## 33.3 The Description Meta Tag

The `description` meta tag provides a short summary of the page, often shown as the snippet text under a search result.

```html
<meta name="description" content="Fresh-baked bread, pastries, and custom cakes in Springfield. Order online or visit our bakery downtown.">
```

Keep descriptions concise (roughly 150–160 characters) and specific to the page — a generic description repeated across every page of a site provides little value to search engines or users.

[Previous](./[32]-The-Head-Element-in-Depth.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[34]-Open-Graph-and-Social-Meta-Tags.md)
