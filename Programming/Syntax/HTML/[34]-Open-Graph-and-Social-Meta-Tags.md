[Previous](./[33]-Meta-Tags.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[35]-Favicons-and-App-Icons.md)

*Metadata & SEO*

# Lesson 34 - Open Graph & Social Media Meta Tags

## 34.1 Open Graph Basics

**Open Graph** (OG) tags control how a page looks when shared as a link on platforms like Facebook, LinkedIn, or Discord — the preview image, title, and description shown in the card.

```html
<meta property="og:title" content="Fresh-Baked Bread — Acme Bakery">
<meta property="og:description" content="Order fresh bread and pastries online, baked daily.">
<meta property="og:image" content="https://acmebakery.com/social-preview.jpg">
<meta property="og:url" content="https://acmebakery.com/">
<meta property="og:type" content="website">
```

Without these tags, social platforms often guess at a title and image, sometimes picking something irrelevant from the page.

---

## 34.2 Twitter Card Tags

Twitter/X uses its own, similar set of tags (though it also falls back to Open Graph tags if these aren't present).

```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Fresh-Baked Bread — Acme Bakery">
<meta name="twitter:description" content="Order fresh bread and pastries online, baked daily.">
<meta name="twitter:image" content="https://acmebakery.com/social-preview.jpg">
```

`twitter:card` values include `summary` (small thumbnail) and `summary_large_image` (large, prominent image) — the latter is generally preferred for visual content.

A good practice is to include a full-sized, correctly proportioned `og:image` (typically 1200×630px) so previews look sharp across every platform rather than stretched or cropped awkwardly.

[Previous](./[33]-Meta-Tags.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[35]-Favicons-and-App-Icons.md)
