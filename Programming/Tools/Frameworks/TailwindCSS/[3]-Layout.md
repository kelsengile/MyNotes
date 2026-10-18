[Previous](./[2]-Utility-First-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[4]-Spacing-Sizing-And-Borders.md)

*Basics*

# Lesson 3 - Layout

## 3.1 Display and containers

**Display utilities** control how an element participates in layout:

| Class | CSS |
|---|---|
| `block` | `display: block` |
| `inline` | `display: inline` |
| `inline-block` | `display: inline-block` |
| `flex` | `display: flex` |
| `inline-flex` | `display: inline-flex` |
| `grid` | `display: grid` |
| `hidden` | `display: none` |
| `contents` | `display: contents` |

```html
<span class="inline-block rounded bg-gray-200 px-2">Badge</span>
<div class="hidden md:block">Only visible from the md breakpoint up</div>
```

**Containers.** The `container` class sets `width: 100%` and applies a `max-width` that matches the current breakpoint. It does **not** center itself or add padding, so add those yourself:

```html
<div class="container mx-auto px-4">
  Page content
</div>
```

**The more common pattern** is a `max-w-*` plus `mx-auto`. It gives you a fixed maximum width that is centered:

```html
<main class="mx-auto max-w-5xl px-4">
  Page content
</main>
```

`mx-auto` sets left and right margins to `auto`, which centers a block that has a width limit.

**Box sizing.** Tailwind's Preflight sets `box-sizing: border-box` on everything, so padding and borders are included in an element's width. This makes sizing predictable.

---

## 3.2 Flexbox utilities

Flexbox lays items out in one direction (a row or a column). Add `flex` to the **parent**, then control children with more utilities.

**Main utilities**

| Purpose | Classes |
|---|---|
| Direction | `flex-row` (default), `flex-col`, `flex-row-reverse` |
| Wrapping | `flex-wrap`, `flex-nowrap` |
| Align along main axis | `justify-start`, `justify-center`, `justify-end`, `justify-between`, `justify-around`, `justify-evenly` |
| Align across axis | `items-start`, `items-center`, `items-end`, `items-stretch`, `items-baseline` |
| Gaps | `gap-4`, `gap-x-2`, `gap-y-6` |
| Child sizing | `flex-1`, `grow`, `shrink-0`, `basis-1/2` |
| Child order | `order-1`, `order-first`, `order-last` |
| Child self-alignment | `self-start`, `self-center`, `self-end` |

**Example: a navbar**

```html
<nav class="flex items-center justify-between bg-white px-6 py-4 shadow">
  <a href="/" class="text-lg font-bold">Brand</a>
  <ul class="flex items-center gap-6 text-gray-600">
    <li><a href="/docs">Docs</a></li>
    <li><a href="/pricing">Pricing</a></li>
    <li><a href="/login" class="rounded bg-blue-600 px-3 py-1 text-white">Log in</a></li>
  </ul>
</nav>
```

**Example: a sidebar plus content**

```html
<div class="flex min-h-screen">
  <aside class="w-64 shrink-0 bg-gray-100 p-4">Sidebar</aside>
  <main class="flex-1 p-6">Content takes the remaining width</main>
</div>
```

**What `flex-1` means:** the item grows and shrinks to share available space equally with other `flex-1` items.

**Perfect centering**

```html
<div class="flex h-screen items-center justify-center">
  <p>Centered both ways</p>
</div>
```

---

## 3.3 Grid utilities

CSS Grid lays items out in rows **and** columns at once. Use it for page layouts, card galleries, and dashboards.

**Main utilities**

| Purpose | Classes |
|---|---|
| Define columns | `grid-cols-1` through `grid-cols-12`, `grid-cols-none`, `grid-cols-subgrid` |
| Define rows | `grid-rows-3`, `grid-rows-none` |
| Gaps | `gap-4`, `gap-x-6`, `gap-y-2` |
| Child spans | `col-span-2`, `col-span-full`, `row-span-2` |
| Child placement | `col-start-2`, `col-end-4` |
| Auto sizing | `auto-cols-fr`, `auto-rows-min` |
| Flow | `grid-flow-row`, `grid-flow-col` |
| Alignment | `place-items-center`, `place-content-center` |

**Example: a 3-column card grid**

```html
<div class="grid grid-cols-3 gap-6">
  <div class="rounded-lg bg-white p-4 shadow">1</div>
  <div class="rounded-lg bg-white p-4 shadow">2</div>
  <div class="rounded-lg bg-white p-4 shadow">3</div>
</div>
```

**Example: items spanning columns**

```html
<div class="grid grid-cols-4 gap-4">
  <div class="col-span-3 bg-blue-100 p-4">Wide (3 of 4 columns)</div>
  <div class="bg-green-100 p-4">Narrow</div>
  <div class="col-span-full bg-gray-100 p-4">Full row</div>
</div>
```

**Auto-fitting grid (no breakpoints needed)**

```html
<div class="grid grid-cols-[repeat(auto-fit,minmax(16rem,1fr))] gap-6">
  <!-- Cards wrap automatically based on available width -->
</div>
```

**Flex or grid?** Use flexbox for one-dimensional layouts (a row of buttons, a navbar). Use grid when you need rows and columns to line up (page layouts, galleries).

---

## 3.4 Positioning and z-index

**Position utilities**

| Class | Behavior |
|---|---|
| `static` | Default flow |
| `relative` | In flow, and becomes a reference point for absolute children |
| `absolute` | Removed from flow, positioned relative to the nearest positioned ancestor |
| `fixed` | Positioned relative to the viewport |
| `sticky` | Scrolls normally until it reaches an offset, then sticks |

**Offsets:** `top-0`, `right-4`, `bottom-0`, `left-1/2`, `inset-0` (all four sides), `inset-x-0`, `inset-y-0`. Negative values work too: `-top-2`.

**Example: a notification badge**

```html
<button class="relative rounded bg-gray-200 px-4 py-2">
  Inbox
  <span class="absolute -right-2 -top-2 flex size-5 items-center justify-center rounded-full bg-red-500 text-xs text-white">
    3
  </span>
</button>
```

The badge is `absolute`, and its parent is `relative`, so the badge is placed relative to the button.

**Example: a sticky header**

```html
<header class="sticky top-0 z-50 bg-white/90 backdrop-blur">
  Stays at the top while scrolling
</header>
```

**Example: a full-cover overlay**

```html
<div class="relative h-64">
  <img src="photo.jpg" alt="" class="h-full w-full object-cover" />
  <div class="absolute inset-0 bg-black/40"></div>
</div>
```

**z-index:** `z-0`, `z-10`, `z-20`, `z-30`, `z-40`, `z-50`, `z-auto`, plus arbitrary values such as `z-[100]`. Higher numbers appear in front.

**Gotchas**

- `z-*` only works on **positioned** elements (anything but `static`) and on flex/grid children.
- A parent that creates a *stacking context* (for example with `opacity`, `transform`, or its own `z-*`) traps its children's z-index inside it.
- Use `overflow-hidden`, `overflow-auto`, or `overflow-x-scroll` to control content that spills out of its box.

---

[Previous](./[2]-Utility-First-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[4]-Spacing-Sizing-And-Borders.md)