[Previous](./[31]-File-Uploads.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[33]-Meta-Tags.md)

*Metadata & SEO*

# Lesson 32 - The `<head>` Element in Depth

## 32.1 The `<title>` Element

`<title>` sets the text shown in the browser tab, bookmarks, and search engine result listings. Every page should have exactly one, and it should be descriptive.

```html
<head>
  <title>Contact Us — Acme Bakery</title>
</head>
```

---

## 32.2 The `<link>` Element

`<link>` connects the document to an external resource — most commonly a stylesheet, but also favicons (Lesson 35) and other resource hints.

```html
<link rel="stylesheet" href="styles.css">
<link rel="preconnect" href="https://fonts.example.com">
```

`rel` describes the relationship between the current page and the linked resource; `href` points to it.

---

## 32.3 `<style>` and `<script>` Placement

`<style>` holds CSS directly in the document, and `<script>` holds or references JavaScript. Both *can* live in the `<head>`, though placement affects behavior:

```html
<head>
  <style>
    body { font-family: sans-serif; }
  </style>
  <script src="analytics.js"></script>
</head>
```

- CSS in `<style>` or linked via `<link>` should generally load in the `<head>`, so the page doesn't flash unstyled content.
- `<script>` tags block HTML parsing by default while they download and run. For scripts that aren't needed immediately, placing them at the end of `<body>`, or using the `defer` attribute, keeps the page rendering quickly (covered further in Lesson 49).

```html
<script src="app.js" defer></script>
```

[Previous](./[31]-File-Uploads.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[33]-Meta-Tags.md)
