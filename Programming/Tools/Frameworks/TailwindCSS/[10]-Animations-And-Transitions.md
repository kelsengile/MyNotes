[Previous](./[9]-Dark-Mode.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[11]-Theme-Customization.md)

*Responsive And Interactive Design*

# Lesson 10 - Animations And Transitions

## 10.1 Transition utilities

A **transition** smoothly animates a property change (for example on hover) instead of switching instantly. Add a transition class, then a state change that alters a property.

```html
<button class="bg-blue-600 text-white transition-colors duration-300 hover:bg-blue-800">
  Smooth color change
</button>
```

**Which properties transition**

| Class | Properties animated |
|---|---|
| `transition` | A common set (colors, opacity, shadow, transform, and others) |
| `transition-colors` | Color-related properties only |
| `transition-opacity` | Opacity |
| `transition-shadow` | Box shadow |
| `transition-transform` | Transform |
| `transition-all` | Everything (convenient, but can be wasteful) |
| `transition-none` | Disable |

**Duration, easing, and delay**

| Purpose | Classes |
|---|---|
| Duration | `duration-75`, `duration-150`, `duration-300`, `duration-500`, `duration-1000` |
| Easing | `ease-linear`, `ease-in`, `ease-out`, `ease-in-out` |
| Delay | `delay-75`, `delay-150`, `delay-300` |

The default is 150ms with an ease-in-out style curve, which suits most interface changes.

```html
<div class="opacity-0 transition-opacity duration-500 ease-out delay-100 group-hover:opacity-100">
  Fades in after a short delay
</div>
```

**Respect users who prefer less motion:**

```html
<div class="transition-transform hover:scale-105 motion-reduce:transition-none motion-reduce:hover:scale-100">
  Card
</div>
```

**Tips**

- Put `transition` on the *base* element (not only on the `hover:` version) so the animation also plays when the state ends.
- Prefer `transition-colors` or `transition-transform` over `transition-all` for better performance.
- Keep interface transitions short (100 to 300ms).

---

## 10.2 Transforms

Transforms move, scale, rotate, or skew an element without affecting the layout of its neighbors.

| Purpose | Classes |
|---|---|
| Scale | `scale-95`, `scale-100`, `scale-105`, `scale-150`, `scale-x-75` |
| Rotate | `rotate-3`, `rotate-45`, `rotate-90`, `-rotate-6`, `rotate-180` |
| Translate | `translate-x-2`, `-translate-y-1`, `translate-x-1/2`, `-translate-x-full` |
| Skew | `skew-x-6`, `skew-y-3` |
| Origin | `origin-center`, `origin-top-left`, `origin-bottom` |

In v4 you do not need a separate `transform` class. Each utility works on its own and they combine.

**Example: a "lift" hover effect**

```html
<div class="rounded-xl bg-white p-6 shadow transition duration-200 hover:-translate-y-1 hover:shadow-xl">
  Lifts slightly on hover
</div>
```

**Example: a button press**

```html
<button class="rounded bg-indigo-600 px-4 py-2 text-white transition active:scale-95">Press me</button>
```

**Example: a rotating chevron**

```html
<button class="group flex items-center gap-2">
  Details
  <svg class="size-4 transition-transform group-hover:rotate-180" viewBox="0 0 20 20" fill="currentColor">
    <path d="M5.5 7.5 10 12l4.5-4.5" stroke="currentColor" fill="none" stroke-width="2" />
  </svg>
</button>
```

**Centering with transforms (a classic technique):**

```html
<div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2">Perfectly centered</div>
```

**3D transforms (v4):** `rotate-x-12`, `rotate-y-12`, `perspective-distant`, and `transform-3d` allow tilt effects.

---

## 10.3 Built-in animations

Tailwind includes four ready-made looping animations.

| Class | Effect | Typical use |
|---|---|---|
| `animate-spin` | Continuous rotation | Loading spinners |
| `animate-ping` | Expanding, fading pulse | Notification dots |
| `animate-pulse` | Gentle fade in and out | Skeleton loaders |
| `animate-bounce` | Bouncing | "Scroll down" hints |
| `animate-none` | Disable | Overrides |

**Example: a spinner**

```html
<svg class="size-6 animate-spin text-blue-600" viewBox="0 0 24 24" fill="none">
  <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
  <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v4a4 4 0 00-4 4H4z" />
</svg>
```

**Example: a live-status dot**

```html
<span class="relative flex size-3">
  <span class="absolute inline-flex h-full w-full animate-ping rounded-full bg-green-400 opacity-75"></span>
  <span class="relative inline-flex size-3 rounded-full bg-green-500"></span>
</span>
```

**Example: a skeleton loader**

```html
<div class="animate-pulse space-y-3">
  <div class="h-4 w-3/4 rounded bg-gray-200"></div>
  <div class="h-4 w-1/2 rounded bg-gray-200"></div>
</div>
```

**Respecting motion preferences**

```html
<div class="motion-safe:animate-bounce">Only animates if the user has not asked for reduced motion</div>
```

**Use animation sparingly.** Continuous motion draws attention and can distract or cause discomfort. Reserve it for meaningful feedback such as loading and status.

---

## 10.4 Custom keyframes

For anything not built in, define your own animation in the theme. In v4, `@keyframes` can live inside `@theme`, next to the animation variable that uses it.

```css
@import "tailwindcss";

@theme {
  --animate-wiggle: wiggle 1s ease-in-out infinite;

  @keyframes wiggle {
    0%, 100% { transform: rotate(-3deg); }
    50% { transform: rotate(3deg); }
  }
}
```

Tailwind creates the utility `animate-wiggle` from the `--animate-wiggle` variable:

```html
<span class="inline-block animate-wiggle">🔔</span>
```

**Example: a fade-up entrance**

```css
@theme {
  --animate-fade-up: fade-up 0.5s ease-out both;

  @keyframes fade-up {
    from { opacity: 0; transform: translateY(1rem); }
    to   { opacity: 1; transform: translateY(0); }
  }
}
```

```html
<h1 class="animate-fade-up">Welcome</h1>
```

**Stagger with delay utilities**, using the same animation on several items:

```html
<li class="animate-fade-up">One</li>
<li class="animate-fade-up [animation-delay:150ms]">Two</li>
<li class="animate-fade-up [animation-delay:300ms]">Three</li>
```

**Entry animations without keyframes (v4).** The `starting:` variant sets the style an element begins from when it first appears (for example, a popover or element that was `display: none`):

```html
<div class="opacity-100 transition-opacity duration-300 starting:opacity-0">Fades in on appearance</div>
```

**In v3,** custom animations were declared in `tailwind.config.js` under `theme.extend.keyframes` and `theme.extend.animation`.

---

[Previous](./[9]-Dark-Mode.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[11]-Theme-Customization.md)