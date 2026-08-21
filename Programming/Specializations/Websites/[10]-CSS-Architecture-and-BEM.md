[Previous](./[9]-CSS-Preprocessors.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[11]-CSS-Frameworks.md)

*Styling at Scale*

# Lesson 10 - CSS Architecture & Naming Conventions (BEM)

## 10.1 The Problem: CSS Doesn't Scale on Its Own

CSS selectors are **globally scoped** — a class name written anywhere can accidentally override styles anywhere else. As a project grows, this leads to specificity wars, styles that break when reordered, and fear of deleting anything. Naming conventions exist to impose structure CSS doesn't enforce on its own.

---

## 10.2 BEM: Block, Element, Modifier

**BEM** names classes around three concepts:

- **Block** — a standalone component (`card`)
- **Element** — a part of that block, joined with `__` (`card__title`)
- **Modifier** — a variation, joined with `--` (`card--featured`)

```html
<div class="card card--featured">
  <h2 class="card__title">Title</h2>
  <p class="card__body">Body text</p>
</div>
```

```css
.card { border: 1px solid #ddd; }
.card__title { font-size: 1.25rem; }
.card--featured { border-color: gold; }
```

Every class name tells you exactly what it belongs to and what role it plays, without needing to trace it back through nested HTML.

---

## 10.3 Flat Specificity by Design

BEM intentionally avoids nested selectors like `.card .title` in CSS. Every rule targets exactly one class, so specificity stays flat and predictable — no rule can accidentally "win" over another due to nesting depth. This trades some CSS terseness for long-term maintainability.

---

## 10.4 Component-Scoped Styles as an Alternative

Modern frameworks (Lesson 17+) often offer their own scoping mechanisms — CSS Modules, styled-components, or Vue's `<style scoped>` — that generate unique class names automatically so styles can't leak between components at all. These solve the same problem BEM solves by convention, but enforce it at the tooling level instead of relying on developer discipline.

---

## 10.5 Utility-First as a Different Philosophy

An alternative approach, popularized by frameworks like Tailwind CSS (Lesson 11), skips custom class names almost entirely in favor of small, single-purpose utility classes (`flex`, `p-4`, `text-center`) applied directly in HTML. There's no one correct architecture — BEM, scoped styles, and utility-first all solve CSS's scaling problem differently, and teams choose based on project size and preference.

---

[Previous](./[9]-CSS-Preprocessors.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[11]-CSS-Frameworks.md)
