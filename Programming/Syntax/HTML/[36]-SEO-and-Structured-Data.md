[Previous](./[35]-Favicons-and-App-Icons.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[37]-Accessibility-Fundamentals.md)

*Metadata & SEO*

# Lesson 36 - SEO Fundamentals & Structured Data

## 36.1 SEO Basics Recap

Good on-page SEO relies heavily on things this course has already covered:

- One clear `<h1>` and a logical heading hierarchy (Lesson 5).
- Semantic elements like `<article>` and `<nav>` (Lessons 11–12).
- Descriptive `alt` text on images (Lesson 16).
- An accurate `<title>` and `<meta name="description">` (Lessons 32–33).

Search engines reward pages that are well-structured and genuinely descriptive far more than pages stuffed with keywords.

---

## 36.2 What Is `schema.org`?

**schema.org** is a shared vocabulary for describing content in a machine-readable way — things like recipes, products, events, and reviews — so search engines can display richer results (like star ratings or prices directly in search listings).

---

## 36.3 JSON-LD Example

The most common way to add schema.org data to a page is **JSON-LD**: a `<script>` tag containing structured JSON, placed anywhere in the document (commonly the `<head>`).

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Sourdough Loaf",
  "image": "https://acmebakery.com/sourdough.jpg",
  "description": "Traditional sourdough bread, baked fresh daily.",
  "offers": {
    "@type": "Offer",
    "priceCurrency": "USD",
    "price": "6.50"
  }
}
</script>
```

Because JSON-LD lives in a `<script>` tag, it doesn't affect the visible page content or layout at all — it exists purely to give search engines extra, structured context about what's on the page.

[Previous](./[35]-Favicons-and-App-Icons.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[37]-Accessibility-Fundamentals.md)
