[Previous](./[32]-Progressive-Web-Apps.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[34]-SEO-Fundamentals.md)

*Best Practices*

# Lesson 33 - Web Accessibility (a11y)

## 33.1 What Accessibility Means

**Accessibility** (often abbreviated **a11y** — "a," 11 letters, "y") means designing and building sites that people with disabilities can use — including visual, motor, auditory, and cognitive disabilities. This includes people using screen readers, keyboard-only navigation, voice control, or magnification, and building for it well tends to improve usability for everyone.

---

## 33.2 Semantic HTML First

The single biggest accessibility win is using the correct HTML element for the job rather than generic `<div>`s for everything:

```html
<!-- Bad: no semantic meaning, no keyboard support -->
<div onclick="submit()">Submit</div>

<!-- Good: real button, keyboard-accessible, announced correctly by screen readers -->
<button onclick="submit()">Submit</button>
```

Elements like `<nav>`, `<header>`, `<main>`, `<button>`, and `<label>` all carry built-in meaning and behavior that screen readers and browsers already understand — recreating that from scratch with `<div>`s is error-prone and usually unnecessary.

---

## 33.3 ARIA, Used Sparingly

**ARIA (Accessible Rich Internet Applications)** attributes add accessibility information HTML can't express on its own, mainly needed for custom interactive widgets:

```html
<div role="alert">Your changes have been saved.</div>
<button aria-expanded="false" aria-controls="menu">Menu</button>
```

The first rule of ARIA is to prefer semantic HTML instead when a native element already does the job — misused ARIA can make accessibility worse than having none at all.

---

## 33.4 Keyboard Navigation

Every interactive element should be reachable and operable using only a keyboard (`Tab` to move focus, `Enter`/`Space` to activate). Avoid removing focus outlines with CSS (`outline: none`) without providing a clear visible alternative — doing so makes it impossible for keyboard users to tell what's focused.

---

## 33.5 Alt Text and Color Contrast

Images need descriptive `alt` text for users who can't see them:

```html
<img src="chart.png" alt="Bar chart showing revenue growth from 2022 to 2024" />
```

Purely decorative images should use `alt=""` so screen readers skip them entirely. Text should also maintain sufficient **color contrast** against its background (a widely used guideline is a 4.5:1 ratio for normal text) so it remains readable for users with low vision or color blindness.

---

## 33.6 Testing Accessibility

Automated tools (axe, Lighthouse's accessibility audit) catch a meaningful subset of issues — missing alt text, poor contrast, missing form labels — but can't catch everything. Manually testing with a keyboard only, and periodically with a screen reader (VoiceOver on macOS, NVDA on Windows), reveals issues automated tools miss entirely.

---

[Previous](./[32]-Progressive-Web-Apps.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[34]-SEO-Fundamentals.md)
