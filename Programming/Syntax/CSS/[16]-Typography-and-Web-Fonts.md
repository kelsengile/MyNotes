[Previous](./[15]-Units.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[17]-Text-Styling.md)

*Colors, Units & Typography*

# Lesson 16 - Typography: Fonts, Font Faces And Web Fonts

## 16.1 The `font-family` Property

`font-family` accepts a comma-separated **stack** of fonts. The browser uses the first one it finds available, falling back to the next if not. Always end a stack with a generic family (`serif`, `sans-serif`, `monospace`) as a safety net.

```css
body {
  font-family: "Helvetica Neue", Arial, sans-serif;
}
```

Font names with spaces should be quoted.

---

## 16.2 Font Weight And Style

```css
p {
  font-weight: 400;   /* normal */
  font-weight: 700;   /* bold */
  font-style: italic;
  font-style: normal;
}
```

`font-weight` also accepts keywords like `normal` (400) and `bold` (700), but numeric values (100–900) give finer control when a font family provides multiple weights.

---

## 16.3 System Fonts

Using the operating system's native font is fast (no download needed) and feels native to the user's platform.

```css
body {
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
}
```

---

## 16.4 Custom Fonts With `@font-face`

`@font-face` lets you load and name a custom font file, which can then be referenced like any other `font-family`.

```css
@font-face {
  font-family: "Inter";
  src: url("/fonts/Inter-Regular.woff2") format("woff2"),
       url("/fonts/Inter-Regular.woff") format("woff");
  font-weight: 400;
  font-style: normal;
  font-display: swap;
}

body {
  font-family: "Inter", sans-serif;
}
```

- Listing multiple formats lets the browser pick the one it supports; `woff2` is the modern standard, with `woff` as a fallback for older browsers.
- `font-display: swap` tells the browser to show fallback text immediately and swap in the custom font once loaded, avoiding invisible text while fonts download.

---

## 16.5 Google Fonts

Google Fonts hosts free web fonts you can load via a `<link>` tag (or `@import`) instead of self-hosting files.

```html
<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet">
```

```css
body {
  font-family: "Roboto", sans-serif;
}
```

Self-hosting (via `@font-face`) generally loads faster and avoids a third-party request, while services like Google Fonts are quicker to set up — the right choice depends on project needs.

---

## 16.6 Variable Fonts

A variable font packs multiple weights/widths/styles into a single file, controlled with `font-variation-settings` or simply a fine-grained `font-weight`.

```css
@font-face {
  font-family: "Inter Variable";
  src: url("/fonts/Inter-Variable.woff2") format("woff2-variations");
  font-weight: 100 900; /* supports the whole range */
}

h1 {
  font-family: "Inter Variable";
  font-weight: 650; /* any value in the supported range */
}
```

[Previous](./[15]-Units.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[17]-Text-Styling.md)
