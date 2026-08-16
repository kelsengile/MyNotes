[Previous](./[11]-Why-Semantics-Matter.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[13]-Div-vs-Span.md)

*Semantic HTML*

# Lesson 12 - Page Structure Elements

## 12.1 `<header>` and `<footer>`

`<header>` represents introductory content — typically a logo, title, or navigation at the top of a page or section. `<footer>` represents closing content — copyright info, links, contact details. Both can be used at the page level or inside individual `<article>`/`<section>` elements.

```html
<header>
  <h1>My Site</h1>
</header>
<footer>
  <p>&copy; 2026 My Site</p>
</footer>
```

---

## 12.2 `<nav>`

`<nav>` wraps a block of primary navigation links. Not every group of links needs `<nav>` — reserve it for major navigation blocks like a main menu or breadcrumb trail.

```html
<nav>
  <ul>
    <li><a href="/">Home</a></li>
    <li><a href="/about.html">About</a></li>
  </ul>
</nav>
```

---

## 12.3 `<main>` and `<section>`

`<main>` wraps the primary content of the page — there should be only one per page, and it should not include repeated content like headers or sidebars. `<section>` groups a thematic chunk of content, usually with its own heading.

```html
<main>
  <section>
    <h2>About Us</h2>
    <p>...</p>
  </section>
  <section>
    <h2>Our Services</h2>
    <p>...</p>
  </section>
</main>
```

---

## 12.4 `<article>` and `<aside>`

`<article>` represents self-contained content that could stand alone or be syndicated elsewhere — a blog post, a news story, a product card. `<aside>` represents content tangentially related to the surrounding content, like a sidebar or pull quote.

```html
<article>
  <h2>Breaking News</h2>
  <p>...</p>
</article>
<aside>
  <h3>Related Links</h3>
  <ul><li><a href="#">See also...</a></li></ul>
</aside>
```

A rule of thumb: if the content would still make sense in an RSS feed by itself, it's probably an `<article>`.

[Previous](./[11]-Why-Semantics-Matter.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[13]-Div-vs-Span.md)
