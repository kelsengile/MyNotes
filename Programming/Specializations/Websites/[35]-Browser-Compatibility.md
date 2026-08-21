[Previous](./[34]-SEO-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[36]-Security-Basics.md)

*Best Practices*

# Lesson 35 - Browser Compatibility & Progressive Enhancement

## 35.1 Why Browsers Behave Differently

Different browsers (Chrome, Firefox, Safari, Edge) use different underlying engines to parse HTML, apply CSS, and run JavaScript, and they don't all adopt new web standards at the same pace. A feature that works perfectly in one browser can behave differently, or not exist at all, in another — especially older or less common browsers.

---

## 35.2 Checking Compatibility

Before relying on a newer CSS or JavaScript feature, it's standard practice to check **Can I Use** (caniuse.com) or MDN's compatibility tables, which show exactly which browsers and versions support a given feature. This turns "I hope this works everywhere" into a concrete, checkable decision.

---

## 35.3 Progressive Enhancement

**Progressive enhancement** means building a solid, functional baseline experience first, then layering on enhancements for browsers that support them — rather than requiring every feature to work for the site to function at all:

```css
.card {
  display: block; /* works everywhere */
}

@supports (display: grid) {
  .card {
    display: grid; /* only applied where grid is supported */
  }
}
```

A user on an older browser gets a simpler but still usable layout, rather than a broken page.

---

## 35.4 Polyfills and Transpilation

A **polyfill** is code that implements a missing feature for browsers that don't support it natively (e.g. adding `fetch` support to a very old browser). **Transpiling** (via tools like Babel, often built into the bundlers from Lesson 13) converts modern JavaScript syntax into an older, more widely supported equivalent, so developers can write current syntax while still shipping code that runs on older browsers.

---

## 35.5 Deciding What to Support

No project can realistically support every browser and version ever released. Teams typically define a **browser support matrix** — often based on real analytics of who actually visits the site — and use tools like `browserslist` to configure exactly which browsers their transpiler and CSS tooling should target, avoiding wasted effort supporting browsers few or no real users have.

---

[Previous](./[34]-SEO-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[36]-Security-Basics.md)
