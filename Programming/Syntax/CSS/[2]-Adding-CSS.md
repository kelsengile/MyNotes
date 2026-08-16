[Previous](./[1]-What-is-CSS-and-How-Styling-Works.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[3]-CSS-Syntax.md)

*Getting Started*

# Lesson 2 - Adding CSS: Inline, Internal And External Stylesheets

There are three ways to attach CSS to an HTML document. Each has a different scope and different use cases.

## 2.1 Inline Styles

Inline styles are written directly on an HTML element using the `style` attribute. They apply only to that one element.

```html
<p style="color: red; font-weight: bold;">This text is red and bold.</p>
```

Inline styles have very high priority (see Lesson 4) and are generally **discouraged** for real projects — they mix content and presentation, can't be reused, and are hard to maintain. They're mostly useful for quick tests or dynamically generated styles from JavaScript.

---

## 2.2 Internal Stylesheets

An internal stylesheet is written inside a `<style>` tag in the document's `<head>`. It applies to the whole page it's written in.

```html
<head>
  <style>
    p {
      color: red;
      font-weight: bold;
    }
  </style>
</head>
```

This keeps styles out of individual tags, but they still only apply to a single HTML file — they can't be shared across multiple pages.

---

## 2.3 External Stylesheets

An external stylesheet is a separate `.css` file linked into the HTML using a `<link>` tag. This is the standard, recommended approach for real projects.

```html
<head>
  <link rel="stylesheet" href="styles.css">
</head>
```

```css
/* styles.css */
p {
  color: red;
  font-weight: bold;
}
```

External stylesheets can be reused across many pages, cached by the browser for faster load times, and keep your HTML clean and focused purely on content.

---

## 2.4 Choosing The Right Method

| Method | Scope | Best For |
|---|---|---|
| Inline | Single element | Quick tests, dynamic styles from scripts |
| Internal | Single page | Small demos, one-off pages |
| External | Any linked page | Real projects, multi-page sites |

In production, external stylesheets are the standard. The rest of this course assumes styles live in an external `.css` file unless stated otherwise.

[Previous](./[1]-What-is-CSS-and-How-Styling-Works.md) | [Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[3]-CSS-Syntax.md)
