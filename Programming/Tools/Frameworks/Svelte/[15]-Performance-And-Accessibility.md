[Previous](./[14]-Testing-Svelte-Applications.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[16]-Building-And-Deployment.md)

*Quality And Delivery*

# Lesson 15 - Performance And Accessibility

## 15.1 Compile-time warnings

Because Svelte is a compiler, it can warn you about problems **before the app runs**. Warnings appear in the terminal, the browser overlay, and your editor.

Common examples:

| Warning | Meaning |
|---|---|
| `a11y_missing_attribute` | An element lacks a required attribute, such as `alt` on `<img>` |
| `a11y_click_events_have_key_events` | A click handler on a non-interactive element has no keyboard equivalent |
| `a11y_no_static_element_interactions` | A `<div>` with an event handler needs a role |
| `css_unused_selector` | A CSS selector matches nothing in this component |
| `state_referenced_locally` | A `$state` value is read once where it will not update |

Example of a fix:

```svelte
<!-- Warns: missing alt -->
<img src="/cat.jpg" />

<!-- Good -->
<img src="/cat.jpg" alt="A grey cat sleeping on a sofa" />

<!-- Warns: click on a div -->
<div onclick={save}>Save</div>

<!-- Good: a real button is focusable and keyboard friendly -->
<button onclick={save}>Save</button>
```

Treat warnings as helpful advice. You can silence one deliberately with a comment, but only when you are sure it is a false alarm:

```svelte
<!-- svelte-ignore a11y_missing_attribute -->
<img src={decorativeUrl} />
```

---

## 15.2 Bundle size

Your **bundle** is the JavaScript the browser must download. Smaller bundles load faster, especially on slow phones.

Built-in help:

- Svelte's runtime is tiny, and unused code is removed (**tree-shaking**).
- SvelteKit **splits code per route**, so a page only loads what it needs.
- Prerendering lets visitors see content before JavaScript arrives.

Things you can do:

- **Inspect the bundle.** Tools such as `rollup-plugin-visualizer` show what takes the most space.
- **Lazy-load heavy components** with a dynamic import:

```svelte
<script>
  let showChart = $state(false);
</script>

<button onclick={() => (showChart = true)}>Show chart</button>

{#if showChart}
  {#await import('./HeavyChart.svelte') then { default: HeavyChart }}
    <HeavyChart />
  {/await}
{/if}
```

- **Choose small dependencies.** Import only what you use (`import debounce from 'lodash/debounce'` rather than the entire library).
- **Use `$state.raw`** for large data you never mutate in place, so Svelte does not wrap it in deep proxies.
- **Key your `{#each}` blocks** so lists update efficiently.
- **Avoid unnecessary `$effect`**. Computed values with `$derived` are cheaper and simpler.
- Measure before and after changes, for example with Lighthouse in Chrome DevTools.

---

## 15.3 Accessible components

**Accessibility (a11y)** means people with different abilities, including keyboard-only users and screen reader users, can use your app. Svelte's warnings help, but you must design for it.

Core habits:

1. **Use semantic HTML.** `<button>`, `<a>`, `<nav>`, `<main>`, `<h1>` to `<h6>`, and `<label>` come with built-in keyboard and screen reader behavior.
2. **Label every form control.**
3. **Give images meaningful `alt` text** (use `alt=""` for purely decorative images).
4. **Make everything reachable by keyboard** and keep a visible focus outline.
5. **Keep enough color contrast** between text and background.
6. **Respect reduced motion**, since some users get discomfort from animation.
7. **Give every page a unique `<title>`.**

An accessible form field and a motion-friendly style:

```svelte
<svelte:head>
  <title>Contact us</title>
</svelte:head>

<label for="email">Email address</label>
<input id="email" type="email" required aria-describedby="email-help" />
<p id="email-help">We never share your email.</p>

<button aria-label="Close dialog" onclick={close}>×</button>

<style>
  @media (prefers-reduced-motion: reduce) {
    * {
      animation: none !important;
      transition: none !important;
    }
  }
</style>
```

Use ARIA attributes (`aria-label`, `aria-expanded`, `role`) only when plain HTML cannot express what you mean. The first rule of ARIA is: do not use ARIA if a native element does the job.

SvelteKit helps by announcing page changes to screen readers and managing focus after navigation. Test with the keyboard alone (Tab, Shift+Tab, Enter, Space, Escape), and try a screen reader sometimes.

---

## 15.4 Image and asset handling

Images are often the heaviest part of a page.

**Where files go:**

| Location | Behavior |
|---|---|
| `static/` | Copied as-is and served from the site root: `static/logo.png` is `/logo.png` |
| `src/lib/assets/` (imported) | Processed by Vite, given a hashed file name for long-term caching |

```svelte
<script>
  import logo from '$lib/assets/logo.png';
</script>

<img src={logo} alt="Company logo" width="120" height="40" />
```

**Enhanced images.** The `@sveltejs/enhanced-img` package creates modern formats (AVIF, WebP) and multiple sizes automatically.

```bash
npm install -D @sveltejs/enhanced-img
```

```js
// vite.config.js
import { sveltekit } from '@sveltejs/kit/vite';
import { enhancedImages } from '@sveltejs/enhanced-img';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [enhancedImages(), sveltekit()] // enhancedImages goes first
});
```

```svelte
<enhanced:img src="$lib/assets/hero.jpg" alt="Mountains at sunrise" />
```

General tips:

- Set `width` and `height` so the browser reserves space and the layout does not jump.
- Use `loading="lazy"` for images that start off-screen.
- Compress images and prefer modern formats.
- Load only the font weights you use, and use `font-display: swap` so text appears immediately.

[Previous](./[14]-Testing-Svelte-Applications.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[16]-Building-And-Deployment.md)