[Previous](./[5]-Typography-And-Colors.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[7]-State-Variants.md)

*Basics*

# Lesson 6 - Backgrounds And Effects

## 6.1 Background utilities

**Background color** was covered in Lesson 5 (`bg-white`, `bg-blue-500/20`). Here are the other background properties.

**Background images**

```html
<div class="h-64 bg-[url(/images/hero.jpg)] bg-cover bg-center bg-no-repeat"></div>
```

Tailwind has no built-in image presets, so use an arbitrary value for the URL.

| Property | Classes |
|---|---|
| Size | `bg-auto`, `bg-cover` (fill, may crop), `bg-contain` (fit, may leave gaps) |
| Position | `bg-center`, `bg-top`, `bg-bottom`, `bg-left`, `bg-right`, `bg-top-left`, and so on |
| Repeat | `bg-repeat`, `bg-no-repeat`, `bg-repeat-x`, `bg-repeat-y` |
| Attachment | `bg-fixed` (parallax-like), `bg-scroll`, `bg-local` |
| Clip | `bg-clip-border`, `bg-clip-padding`, `bg-clip-content`, `bg-clip-text` |
| Blend | `bg-blend-multiply`, `bg-blend-overlay`, `bg-blend-screen` |

**Example: a hero section with a tinted image**

```html
<section class="bg-gray-900 bg-[url(/images/hero.jpg)] bg-cover bg-center bg-blend-multiply">
  <div class="mx-auto max-w-3xl px-6 py-32 text-center text-white">
    <h1 class="text-5xl font-bold">Welcome</h1>
  </div>
</section>
```

The dark `bg-gray-900` color blends with the image through `bg-blend-multiply`, which darkens it so the text is readable.

**Pattern tip:** decorative patterns made from CSS gradients are lighter than image files.

```html
<div class="h-40 bg-[radial-gradient(circle,#cbd5e1_1px,transparent_1px)] bg-size-[16px_16px]"></div>
```

---

## 6.2 Shadows

**Box shadows**

| Class | Look |
|---|---|
| `shadow-2xs`, `shadow-xs` | Barely there |
| `shadow-sm` | Subtle |
| `shadow-md` | Standard card |
| `shadow-lg` | Raised, such as a dropdown |
| `shadow-xl`, `shadow-2xl` | Prominent, such as a modal |
| `shadow-none` | Remove |

(In v3, `shadow-sm` was the smallest and plain `shadow` the next step. v4 shifted names: v3 `shadow-sm` is now `shadow-xs`, and v3 `shadow` is now `shadow-sm`.)

```html
<div class="rounded-xl bg-white p-6 shadow-md hover:shadow-xl transition-shadow">Hover me</div>
```

**Colored shadows**

```html
<button class="rounded-lg bg-indigo-600 px-4 py-2 text-white shadow-lg shadow-indigo-500/50">
  Glow
</button>
```

**Inset shadows** (v4): `inset-shadow-sm` creates an inner shadow, and `inset-shadow-indigo-500/30` colors it.

**Shadows and rings combine** because both use `box-shadow` internally. Tailwind composes them, so `shadow-md ring-1 ring-gray-200` works as expected.

**Drop shadow** (a *filter*) follows the actual shape of transparent PNGs and SVGs, unlike `box-shadow`, which is always rectangular.

```html
<img src="logo.png" alt="Logo" class="drop-shadow-lg" />
```

Size names: `drop-shadow-xs`, `drop-shadow-sm`, `drop-shadow-md`, `drop-shadow-lg`, `drop-shadow-xl`, `drop-shadow-2xl`.

---

## 6.3 Filters and blur

**Filters** change how the element itself (and its children) is painted.

| Class | Effect |
|---|---|
| `blur-sm`, `blur-md`, `blur-lg` | Blur the element |
| `brightness-50`, `brightness-125` | Darken or lighten (100 is normal) |
| `contrast-50`, `contrast-150` | Change contrast |
| `grayscale` | Remove color |
| `sepia` | Warm vintage tint |
| `invert` | Invert colors |
| `hue-rotate-90` | Shift hues |
| `saturate-50`, `saturate-200` | Reduce or boost color |

Combine them freely:

```html
<img src="photo.jpg" alt="" class="grayscale transition hover:grayscale-0 hover:brightness-110" />
```

**Backdrop filters** affect what is *behind* a semi-transparent element, the basis of the "frosted glass" look:

```html
<div class="relative">
  <img src="bg.jpg" alt="" class="h-64 w-full object-cover" />
  <div class="absolute inset-x-4 bottom-4 rounded-xl bg-white/30 p-4 backdrop-blur-md">
    <p class="font-semibold text-white">Frosted caption</p>
  </div>
</div>
```

Other backdrop classes: `backdrop-blur-sm`, `backdrop-brightness-75`, `backdrop-saturate-150`, `backdrop-grayscale`.

**Blend modes:** `mix-blend-multiply`, `mix-blend-screen`, `mix-blend-overlay` blend an element with whatever sits behind it.

**Performance note.** Large blurs and backdrop filters are expensive, especially on low-end phones. Use them on small areas, and avoid animating blur values.

---

## 6.4 Images and object-fit

**Preflight defaults for images.** Images and other media are set to `display: block`, with `max-width: 100%` and `height: auto`, so they never overflow their container.

**`object-fit`** controls how an image fills a box you've sized, like `background-size` for `<img>`.

| Class | Behavior |
|---|---|
| `object-cover` | Fill the box and crop the excess (most common) |
| `object-contain` | Fit entirely, which may leave empty space |
| `object-fill` | Stretch (distorts) |
| `object-none` | Keep original size |
| `object-scale-down` | Shrink only if needed |

**`object-position`:** `object-center`, `object-top`, `object-bottom`, `object-left`, `object-right`. Use `object-top` for portraits so heads are not cropped.

**Example: a consistent card image**

```html
<img
  src="product.jpg"
  alt="Blue running shoe, side view"
  class="aspect-video w-full rounded-lg object-cover"
/>
```

**Example: an avatar**

```html
<img src="user.jpg" alt="Maria Lopez" class="size-12 rounded-full object-cover" />
```

**Always write meaningful `alt` text** (or `alt=""` for purely decorative images). Lesson 16 covers this further.

**Responsive images.** Tailwind handles layout, not file sizes. Use the HTML `srcset` and `sizes` attributes or `<picture>` to serve smaller files to small screens.

---

[Previous](./[5]-Typography-And-Colors.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[7]-State-Variants.md)