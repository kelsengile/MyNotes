[Previous](./[33]-Web-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[35]-Browser-Compatibility.md)

*Best Practices*

# Lesson 34 - SEO Fundamentals

## 34.1 What SEO Is

**Search Engine Optimization (SEO)** is the practice of structuring a site so search engines can find, understand, and rank it well for relevant queries. Search engines work by **crawling** (following links to discover pages), **indexing** (storing and understanding page content), and **ranking** (ordering results for a given search).

---

## 34.2 Title Tags and Meta Descriptions

Two of the most impactful, simplest elements live in a page's `<head>`:

```html
<title>Affordable Web Hosting Plans | ExampleHost</title>
<meta name="description" content="Compare ExampleHost's hosting plans starting at $5/month, with free SSL and 24/7 support." />
```

The `<title>` appears as the clickable headline in search results and browser tabs; the meta description appears as the summary snippet beneath it. Both should be unique per page and accurately describe that specific page's content.

---

## 34.3 Semantic Structure Helps Search Engines Too

The same semantic HTML that helps accessibility (Lesson 33) also helps search engines understand a page's structure: a single `<h1>` describing the page's main topic, logically nested `<h2>`/`<h3>` headings, and meaningful link text (`"our hosting plans"` rather than `"click here"`).

---

## 34.4 Crawlability

Search engines need to actually be able to reach and render your content:

- `robots.txt` at the root tells crawlers which paths they may or may not crawl
- A **sitemap.xml** lists all pages you want indexed, helping crawlers discover them faster
- Content that only appears after client-side JavaScript runs (Lesson 19) can be harder for some crawlers to index reliably compared to content present in the initial HTML — a key motivation behind server-side rendering, covered next

---

## 34.5 Performance and Mobile-Friendliness

Search engines factor in page speed (Lesson 31) and mobile usability directly into ranking. A page that's slow to load or difficult to use on a phone will generally rank worse than an equivalent fast, mobile-friendly page, independent of content quality.

---

## 34.6 Structured Data

**Structured data** (using the Schema.org vocabulary, usually embedded as JSON-LD) gives search engines explicit, machine-readable facts about a page's content — enabling rich results like star ratings or recipe cook times directly in search listings:

```html
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Wireless Mouse",
  "offers": { "@type": "Offer", "price": "24.99", "priceCurrency": "USD" }
}
</script>
```

---

[Previous](./[33]-Web-Accessibility.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[35]-Browser-Compatibility.md)
