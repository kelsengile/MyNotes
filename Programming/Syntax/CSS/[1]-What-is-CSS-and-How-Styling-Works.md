[Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[2]-Adding-CSS.md)

*Getting Started*

# Lesson 1 - What Is CSS And How Styling Works

## 1.1 What Is CSS?

CSS stands for **Cascading Style Sheets**. It's the language used to describe how HTML elements should look and be arranged on a page — colors, fonts, spacing, sizing, positioning, and animation.

HTML gives a page its **structure and content** (headings, paragraphs, images, links). CSS gives that content its **presentation** (how it looks and where it sits). Separating the two means you can completely change a page's appearance without touching the HTML at all.

```html
<h1>Hello World</h1>
```

```css
h1 {
  color: darkblue;
  font-size: 2rem;
  text-align: center;
}
```

---

## 1.2 Why "Cascading"?

The "cascading" part of the name refers to how CSS resolves conflicts when multiple rules target the same element. Styles can come from several places — the browser's default styles, an external stylesheet, an internal `<style>` block, or inline styles — and CSS follows a predictable set of rules (covered in Lesson 4) to decide which one "wins."

Think of it like a waterfall: styles flow down from general, browser-wide defaults to more specific rules written by you, with later and more specific rules typically overriding earlier, more general ones.

---

## 1.3 How The Browser Applies Styles

When a browser loads a page, it roughly follows these steps:

1. **Parse the HTML** into a tree of elements (the DOM).
2. **Parse the CSS** into a set of style rules.
3. **Match** each CSS rule to the elements it applies to.
4. **Compute** the final styles for every element, resolving any conflicts.
5. **Render** the page using those computed styles.

You don't need to manage this process yourself — the browser does it automatically — but understanding that CSS is *matched and computed* (not just "pasted on") helps explain why some styles behave unexpectedly, especially once multiple stylesheets are involved.

---

## 1.4 What You Can Style With CSS

CSS covers far more than colors and fonts. Over this course, you'll learn to control:

- **Layout** — how elements are positioned and arranged (Flexbox, Grid, positioning)
- **Box properties** — size, spacing, and borders of every element
- **Typography** — fonts, text spacing, and readability
- **Visual effects** — backgrounds, shadows, gradients, transitions, and animations
- **Responsiveness** — adapting a design to different screen sizes and devices
- **Theming** — reusable variables for consistent, maintainable designs

By the end of this course, you'll be able to take a plain HTML page and turn it into a fully styled, responsive, production-ready interface.

[Table of Contents](./[0]-Introduction-to-CSS.md) | [Next](./[2]-Adding-CSS.md)
