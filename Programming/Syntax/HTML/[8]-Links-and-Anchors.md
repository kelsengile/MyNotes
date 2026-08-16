[Previous](./[7]-Lists.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[9]-Comments-and-Whitespace.md)

*Text & Content Basics*

# Lesson 8 - Links & Anchors

## 8.1 The Anchor Element

The `<a>` (anchor) element creates a hyperlink. Its most important attribute is `href`, which points to the destination.

```html
<a href="https://example.com">Visit Example</a>
```

Anything can go inside an `<a>` — text, images, even other elements — as long as the nesting rules from Lesson 4 are respected.

---

## 8.2 Relative vs. Absolute URLs

- An **absolute URL** includes the full address, including the protocol and domain: `https://example.com/about.html`.
- A **relative URL** points to a location relative to the current file: `about.html`, `../images/logo.png`, `/contact.html`.

```html
<a href="https://example.com/about.html">Absolute link</a>
<a href="about.html">Relative link (same folder)</a>
<a href="../index.html">Relative link (parent folder)</a>
```

Use relative URLs for links within your own site — they keep working no matter what domain the site is hosted on.

---

## 8.3 Target Attribute

The `target` attribute controls where a link opens. The most common value is `_blank`, which opens the link in a new tab.

```html
<a href="https://example.com" target="_blank" rel="noopener noreferrer">Open in new tab</a>
```

When using `target="_blank"`, it's good practice to also add `rel="noopener noreferrer"` for security — it prevents the new page from gaining access to the original page's `window` object.

---

## 8.4 Linking Within a Page

You can link to a specific spot on the same page using an `id` and a `#` fragment.

```html
<h2 id="section-two">Section Two</h2>
...
<a href="#section-two">Jump to Section Two</a>
```

This is how "back to top" links and in-page table-of-contents navigation are typically built.

[Previous](./[7]-Lists.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[9]-Comments-and-Whitespace.md)
