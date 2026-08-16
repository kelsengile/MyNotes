[Previous](./[34]-Open-Graph-and-Social-Meta-Tags.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[36]-SEO-and-Structured-Data.md)

*Metadata & SEO*

# Lesson 35 - Favicons & App Icons

## 35.1 The Favicon Link

A **favicon** is the small icon shown in a browser tab, bookmarks list, and history. It's declared with a `<link rel="icon">` tag.

```html
<link rel="icon" type="image/png" href="favicon.png">
```

Browsers will also automatically look for a `favicon.ico` file in the site's root folder even without any `<link>` tag, but declaring it explicitly is more reliable and lets you use modern formats like PNG or SVG.

---

## 35.2 Apple Touch Icon

iOS uses a separate icon when a user adds your site to their home screen, specified with `apple-touch-icon`.

```html
<link rel="apple-touch-icon" sizes="180x180" href="apple-touch-icon.png">
```

This should be a square PNG, typically 180×180 pixels, without transparency (iOS adds its own rounded corners and background).

---

## 35.3 Manifest Icons

For a broader set of icon sizes — used by Android home screens and Progressive Web Apps — icons are declared in a separate **web app manifest** file (`manifest.json`), linked from the `<head>`.

```html
<link rel="manifest" href="manifest.json">
```

```json
{
  "icons": [
    { "src": "icon-192.png", "sizes": "192x192", "type": "image/png" },
    { "src": "icon-512.png", "sizes": "512x512", "type": "image/png" }
  ]
}
```

For most simple sites, a favicon and an Apple touch icon cover the essentials; the manifest becomes important once you want your site to behave more like an installable app.

[Previous](./[34]-Open-Graph-and-Social-Meta-Tags.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[36]-SEO-and-Structured-Data.md)
