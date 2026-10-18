[Previous](./[7]-State-Variants.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[9]-Dark-Mode.md)

*Responsive And Interactive Design*

# Lesson 8 - Responsive Design

## 8.1 Mobile-first breakpoints

Tailwind is **mobile-first**. Unprefixed utilities apply at *all* screen sizes, and prefixed ones apply from that breakpoint **and up** (they use `min-width` media queries).

**Default breakpoints**

| Prefix | Minimum width | CSS |
|---|---|---|
| `sm:` | 40rem (640px) | `@media (width >= 40rem)` |
| `md:` | 48rem (768px) | `@media (width >= 48rem)` |
| `lg:` | 64rem (1024px) | `@media (width >= 64rem)` |
| `xl:` | 80rem (1280px) | `@media (width >= 80rem)` |
| `2xl:` | 96rem (1536px) | `@media (width >= 96rem)` |

**Common mistake:** thinking `sm:` means "on small screens". It means "small screens **and larger**". To style mobile, use the unprefixed class, then override upward.

```html
<!-- Mobile: centered text. From md up: left-aligned. -->
<p class="text-center md:text-left">Hello</p>
```

**Required HTML.** For responsive design to work on phones, your page needs this in `<head>`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
```

**Workflow:** design the mobile layout first with plain utilities, then add `sm:`, `md:`, `lg:` overrides only where the layout needs to change. This results in less code than starting with desktop and undoing styles.

---

## 8.2 Responsive utility prefixes

Any utility (and any other variant) can take a breakpoint prefix.

**Example: columns that grow with the screen**

```html
<div class="grid grid-cols-1 gap-4 sm:grid-cols-2 lg:grid-cols-4">
  <div class="rounded bg-white p-4 shadow">1</div>
  <div class="rounded bg-white p-4 shadow">2</div>
  <div class="rounded bg-white p-4 shadow">3</div>
  <div class="rounded bg-white p-4 shadow">4</div>
</div>
```

**Example: show or hide by screen size**

```html
<nav class="hidden md:flex gap-6">Desktop links</nav>
<button class="md:hidden">☰ Menu</button>
```

**Example: sizes that scale**

```html
<h1 class="text-3xl font-bold md:text-5xl lg:text-6xl">Responsive headline</h1>
<section class="px-4 py-8 md:px-8 md:py-16">...</section>
```

**Example: stack on mobile, row on larger**

```html
<div class="flex flex-col gap-4 md:flex-row md:items-center">
  <img src="a.jpg" alt="" class="w-full md:w-48" />
  <p>Text next to the image on wider screens</p>
</div>
```

**Targeting only a range or only below a size**

| Variant | Meaning |
|---|---|
| `max-md:` | Below `md` only |
| `md:max-lg:` | Only between `md` and `lg` |
| `min-[900px]:` | From an exact arbitrary width |
| `max-[600px]:` | Below an exact arbitrary width |

```html
<div class="md:max-xl:grid-cols-2">Two columns only from md until just before xl</div>
```

**Combining with other variants:**

```html
<button class="bg-blue-600 md:hover:bg-blue-700">...</button>
```

---

## 8.3 Container queries

Breakpoints respond to the **viewport**. **Container queries** respond to the size of a **parent element**, so a component adapts to wherever you place it (a wide main column or a narrow sidebar).

Container queries are built into Tailwind v4. (v3 required the `@tailwindcss/container-queries` plugin.)

**Step 1: mark the parent with `@container`.**
**Step 2: use `@sm:`, `@md:`, `@lg:` variants on children.**

```html
<div class="@container">
  <div class="flex flex-col gap-4 @md:flex-row @md:items-center">
    <img src="product.jpg" alt="" class="w-full rounded @md:w-40" />
    <div>
      <h3 class="font-semibold">Product name</h3>
      <p class="text-gray-600">The layout changes at the container's width, not the screen's.</p>
    </div>
  </div>
</div>
```

**Container sizes (default)**

| Variant | Min container width |
|---|---|
| `@3xs` | 16rem |
| `@2xs` | 18rem |
| `@xs` | 20rem |
| `@sm` | 24rem |
| `@md` | 28rem |
| `@lg` | 32rem |
| `@xl` | 36rem |
| `@2xl` to `@7xl` | 42rem to 80rem |

**Other forms**

```html
<!-- Named containers, useful when nesting -->
<div class="@container/main">
  <div class="@lg/main:grid-cols-3 grid"></div>
</div>

<!-- Max-width and arbitrary values -->
<div class="@max-md:hidden @min-[475px]:flex"></div>
```

**When to use which**

- Use **viewport breakpoints** for page-level layout (header, sidebar visibility).
- Use **container queries** for reusable components (cards, widgets) that might appear in different-width places.

---

## 8.4 Custom breakpoints

Define breakpoints in your CSS theme with the `--breakpoint-*` namespace.

**Add a new breakpoint**

```css
@import "tailwindcss";

@theme {
  --breakpoint-3xl: 120rem;
}
```

Now `3xl:flex` works.

**Add one with an unusual name**

```css
@theme {
  --breakpoint-tablet: 50rem;
}
```

```html
<div class="tablet:grid-cols-2"></div>
```

**Replace all defaults.** Reset the namespace with `initial`, then define your own:

```css
@theme {
  --breakpoint-*: initial;
  --breakpoint-phone: 30rem;
  --breakpoint-tablet: 48rem;
  --breakpoint-desktop: 72rem;
}
```

**One-off breakpoints** do not need to be in the theme:

```html
<div class="min-[1100px]:flex max-[480px]:hidden"></div>
```

**Tips**

- Use `rem` units so breakpoints respect users' font-size settings.
- Keep the breakpoint list short. Three to five is enough for almost every site.
- (In v3, breakpoints were set in `tailwind.config.js` under `theme.screens`.)

---

[Previous](./[7]-State-Variants.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[9]-Dark-Mode.md)