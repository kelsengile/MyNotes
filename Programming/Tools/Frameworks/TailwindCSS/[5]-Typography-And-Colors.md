[Previous](./[4]-Spacing-Sizing-And-Borders.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[6]-Backgrounds-And-Effects.md)

*Basics*

# Lesson 5 - Typography And Colors

## 5.1 Font size, weight, and family

**Font size.** Each size class also sets a sensible line height.

| Class | Size |
|---|---|
| `text-xs` | 0.75rem |
| `text-sm` | 0.875rem |
| `text-base` | 1rem |
| `text-lg` | 1.125rem |
| `text-xl` | 1.25rem |
| `text-2xl` to `text-9xl` | Increasingly large |

**Font weight:** `font-thin`, `font-light`, `font-normal`, `font-medium`, `font-semibold`, `font-bold`, `font-extrabold`, `font-black`.

**Font family:** `font-sans`, `font-serif`, `font-mono`. Custom families are added in the theme (Lesson 11).

**Line height** can be a standalone class or attached to the size with a slash:

```html
<p class="text-lg leading-8">Separate line-height</p>
<p class="text-lg/8">Same thing, shorter</p>
```

Other line-height classes: `leading-none`, `leading-tight`, `leading-snug`, `leading-normal`, `leading-relaxed`, `leading-loose`.

**Letter spacing:** `tracking-tighter`, `tracking-tight`, `tracking-normal`, `tracking-wide`, `tracking-widest`.

**Style helpers:** `italic`, `not-italic`, `uppercase`, `lowercase`, `capitalize`, `underline`, `line-through`, `no-underline`.

**Truncating text**

```html
<p class="truncate">This long line is cut off with an ellipsis at the end…</p>
<p class="line-clamp-3">A paragraph limited to three lines, then an ellipsis.</p>
```

**Example: a heading and intro**

```html
<h1 class="text-4xl font-bold tracking-tight text-gray-900">Build faster</h1>
<p class="mt-4 text-lg/8 text-gray-600">Utility classes keep your design consistent.</p>
```

Note that Preflight removes default heading sizes and weights, so an `<h1>` looks like normal text until you style it. Pick the tag for meaning and style it with classes.

---

## 5.2 Text alignment and spacing

**Alignment:** `text-left`, `text-center`, `text-right`, `text-justify`, `text-start`, `text-end`.

**Vertical alignment (inline items):** `align-top`, `align-middle`, `align-bottom`, `align-baseline`.

**Wrapping and breaking**

| Class | Effect |
|---|---|
| `whitespace-nowrap` | Never wrap |
| `whitespace-pre-wrap` | Preserve line breaks and spaces, but wrap |
| `break-words` | Break long words that would overflow |
| `text-balance` | Balance line lengths (great for headings) |
| `text-pretty` | Avoid short last lines (great for paragraphs) |

```html
<h2 class="text-balance text-center text-3xl font-bold">
  A headline that wraps into evenly sized lines
</h2>
```

**Indentation and decoration**

```html
<p class="indent-8">First line indented</p>
<a href="#" class="underline decoration-blue-500 decoration-2 underline-offset-4">Link</a>
```

**Lists.** Preflight removes list bullets. Restore them with:

```html
<ul class="list-disc list-inside space-y-1">
  <li>Bulleted</li>
</ul>
<ol class="list-decimal ps-6">
  <li>Numbered</li>
</ol>
```

**Text transform and color together**

```html
<span class="text-xs font-semibold uppercase tracking-widest text-gray-500">Section label</span>
```

---

## 5.3 The color palette

Tailwind ships a curated palette: **22 color families**, each with shades from `50` (lightest) to `950` (darkest).

**Families:**

- Grays: `slate`, `gray`, `zinc`, `neutral`, `stone`
- Colors: `red`, `orange`, `amber`, `yellow`, `lime`, `green`, `emerald`, `teal`, `cyan`, `sky`, `blue`, `indigo`, `violet`, `purple`, `fuchsia`, `pink`, `rose`
- Specials: `black`, `white`, `transparent`, `current` (the current text color)

**Shades:** `50`, `100`, `200`, `300`, `400`, `500`, `600`, `700`, `800`, `900`, `950`.

**Using a color.** Put a property prefix in front of any color name and shade:

| Prefix | Property |
|---|---|
| `text-` | Text color |
| `bg-` | Background |
| `border-` | Border |
| `ring-` | Ring |
| `outline-` | Outline |
| `divide-` | Dividers |
| `shadow-` | Shadow color |
| `fill-`, `stroke-` | SVG |
| `accent-` | Checkboxes, radios |
| `caret-` | Text cursor |
| `decoration-` | Underline color |
| `placeholder-` | Placeholder text color |

```html
<div class="border border-sky-200 bg-sky-50 p-4 text-sky-900">
  Informational message
</div>
<div class="border border-red-200 bg-red-50 p-4 text-red-800">
  Error message
</div>
```

**Choosing shades**

- Backgrounds: `50` to `200` for subtle areas, `500` to `700` for solid buttons.
- Text: `700` to `900` for body, `500` to `600` for secondary text.
- Hover states: go one or two steps darker (`bg-blue-600 hover:bg-blue-700`).
- Pair a very light background with a very dark shade of the *same* family for tidy alerts.

**Colors in v4** are defined with the modern `oklch()` color space, which gives more vivid, evenly perceived colors on wide-gamut screens. Your usage does not change.

**Arbitrary colors:** `bg-[#0f172a]`, `text-[rgb(34,197,94)]`.

---

## 5.4 Opacity and gradients

**Color opacity with a slash.** This is the most useful form. It changes only the color's transparency, not the whole element.

```html
<div class="bg-black/50 text-white">50% transparent black background</div>
<div class="border border-blue-500/25 text-blue-700/80"></div>
```

**Whole-element opacity:** `opacity-0`, `opacity-25`, `opacity-50`, `opacity-75`, `opacity-100`. This fades the element **and** its children.

**Gradients (v4 names)**

A gradient has a **direction**, a **from** color, an optional **via** color, and a **to** color.

```html
<div class="bg-linear-to-r from-indigo-500 via-purple-500 to-pink-500 p-8 text-white">
  Left to right
</div>
```

| Class | Meaning |
|---|---|
| `bg-linear-to-r` | Left to right (also `-l`, `-t`, `-b`, `-tr`, `-br`, `-bl`, `-tl`) |
| `bg-linear-45` | Any angle |
| `bg-radial` | Radial gradient |
| `bg-conic` | Conic gradient |
| `from-10%`, `via-30%`, `to-90%` | Color stop positions |

In v3, linear gradients were named `bg-gradient-to-r` and so on. v4 still accepts that name for compatibility, but the new names are preferred.

**Gradient text**

```html
<h1 class="bg-linear-to-r from-blue-600 to-cyan-400 bg-clip-text text-5xl font-extrabold text-transparent">
  Gradient headline
</h1>
```

`bg-clip-text` clips the background to the text shape, and `text-transparent` reveals it.

**Fade-out overlay on an image**

```html
<div class="relative">
  <img src="hero.jpg" alt="" class="h-64 w-full object-cover" />
  <div class="absolute inset-0 bg-linear-to-t from-black/70 to-transparent"></div>
</div>
```

---

[Previous](./[4]-Spacing-Sizing-And-Borders.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[6]-Backgrounds-And-Effects.md)