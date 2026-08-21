[Previous](./[30]-CI-CD-for-Web-Projects.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[32]-Progressive-Web-Apps.md)

*Deployment & Production*

# Lesson 31 - Performance Optimization Basics

## 31.1 Why Performance Matters

Slow sites lose users — studies consistently show higher bounce rates and lower conversion as load time increases, and performance is also a ranking factor for search engines (Lesson 34). Performance work generally focuses on two things: getting content to the browser faster, and helping the browser render and respond to it faster once it arrives.

---

## 31.2 Core Web Vitals

Google's **Core Web Vitals** are widely used metrics for real-world user experience:

- **LCP (Largest Contentful Paint)** — how long until the main content is visible
- **INP (Interaction to Next Paint)** — how responsive the page is to user interaction
- **CLS (Cumulative Layout Shift)** — how much content unexpectedly shifts around while loading

These are measurable in Chrome DevTools' Lighthouse panel, giving concrete, prioritized targets instead of a vague sense that "the site feels slow."

---

## 31.3 Reducing What Gets Sent

Smaller payloads load faster on any connection:

- **Minification** — strip whitespace and shorten code (handled by bundlers, Lesson 13)
- **Compression** — servers commonly gzip or brotli-compress responses before sending
- **Image optimization** — use appropriately sized images and modern formats (WebP, AVIF) instead of oversized JPEGs/PNGs
- **Code splitting** — load only the JavaScript a given page actually needs, deferring the rest

---

## 31.4 Lazy Loading

**Lazy loading** defers loading resources until they're actually needed:

```html
<img src="photo.jpg" loading="lazy" alt="A photo" />
```

```jsx
const Chart = lazy(() => import("./Chart.jsx")); // load this component's code on demand
```

This keeps the initial page load light, especially valuable for content far down a long page that many visitors never scroll to.

---

## 31.5 Caching

**Caching** avoids re-fetching or re-computing something unnecessarily. Browsers cache static assets (CSS, JS, images) based on HTTP caching headers (`Cache-Control`), and CDNs (Lesson 28) cache responses close to users geographically. A common pattern is giving build output files unique, content-based filenames (e.g. `main.abc123.js`) so they can be cached aggressively and safely forever, since any code change produces a new filename.

---

[Previous](./[30]-CI-CD-for-Web-Projects.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[32]-Progressive-Web-Apps.md)
