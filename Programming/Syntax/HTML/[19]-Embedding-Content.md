[Previous](./[18]-Audio-and-Video.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[20]-SVG-in-HTML.md)

*Media*

# Lesson 19 - Embedding Content

## 19.1 `<iframe>`

`<iframe>` embeds another HTML document inside the current page — commonly used for maps, videos, or third-party widgets.

```html
<iframe
  src="https://www.example.com/map"
  width="600"
  height="400"
  title="Location map"
  loading="lazy">
</iframe>
```

- Always include a `title` attribute — it's how screen readers describe the embedded content.
- `loading="lazy"` defers loading the iframe until it's about to enter the viewport, improving page performance.
- Be cautious embedding untrusted third-party content; the `sandbox` attribute can restrict what an iframe is allowed to do.

---

## 19.2 `<embed>`

`<embed>` is a simpler, more limited element for embedding external content handled by a browser plugin, such as a PDF viewer.

```html
<embed src="document.pdf" type="application/pdf" width="600" height="800">
```

`<embed>` has no closing tag and no fallback content — if the browser can't display it, nothing appears.

---

## 19.3 `<object>`

`<object>` is a more flexible, older embedding element that supports fallback content between its opening and closing tags.

```html
<object data="document.pdf" type="application/pdf" width="600" height="800">
  <p>Your browser can't display PDFs. <a href="document.pdf">Download it instead</a>.</p>
</object>
```

In practice, `<iframe>` is the most commonly used of the three today. `<embed>` and `<object>` still appear for specific legacy or plugin-based use cases, but modern alternatives (like `<video>`, `<audio>`, or `<img>` for SVG) are usually preferred when available.

[Previous](./[18]-Audio-and-Video.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[20]-SVG-in-HTML.md)
