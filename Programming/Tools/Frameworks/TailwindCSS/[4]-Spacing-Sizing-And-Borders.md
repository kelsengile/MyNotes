[Previous](./[3]-Layout.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[5]-Typography-And-Colors.md)

*Basics*

# Lesson 4 - Spacing, Sizing, And Borders

## 4.1 Padding and margin

**Padding** is space *inside* an element. **Margin** is space *outside* it.

| Prefix | Meaning |
|---|---|
| `p-` / `m-` | All sides |
| `px-` / `mx-` | Left and right |
| `py-` / `my-` | Top and bottom |
| `pt-` `pr-` `pb-` `pl-` | One side (`m` works the same) |
| `ps-` / `pe-` | Start and end (logical, respects text direction; `ms-` and `me-` for margin) |

**The spacing scale.** One unit is `0.25rem` (4px at the default font size):

| Class number | Size | Pixels |
|---|---|---|
| `1` | 0.25rem | 4px |
| `2` | 0.5rem | 8px |
| `4` | 1rem | 16px |
| `6` | 1.5rem | 24px |
| `8` | 2rem | 32px |
| `16` | 4rem | 64px |

So `p-4` is `padding: 1rem`, and `mt-8` is `margin-top: 2rem`. Any number works in v4 (`p-13` is `3.25rem`).

```html
<div class="p-6 mb-4 mx-auto">...</div>
<div class="px-4 py-2">Horizontal 1rem, vertical 0.5rem</div>
```

**Other spacing tools**

- **Negative margins:** `-mt-2`, `-ml-4`.
- **Auto margins:** `mx-auto` centers a block; `ml-auto` pushes an item to the right in a flex row.
- **Arbitrary values:** `p-[13px]`.
- **Space between children:** `space-y-4` adds vertical margin between siblings, and `space-x-4` does so horizontally.

```html
<ul class="space-y-2">
  <li>One</li>
  <li>Two</li>
  <li>Three</li>
</ul>
```

**Prefer `gap-*` with flex or grid** over `space-*`. It handles wrapping and reversed layouts better.

---

## 4.2 Width and height

**Width utilities**

| Class | CSS |
|---|---|
| `w-64` | `width: 16rem` (spacing scale) |
| `w-1/2`, `w-2/3` | Fractions of the parent |
| `w-full` | `100%` |
| `w-screen` | `100vw` |
| `w-fit` | `fit-content` |
| `w-min`, `w-max` | `min-content`, `max-content` |
| `w-[420px]` | Arbitrary |

**Height utilities** follow the same pattern: `h-10`, `h-full`, `h-screen`, `h-dvh` (dynamic viewport height, better on mobile browsers), `h-fit`.

**Min and max**

```html
<div class="min-h-screen max-w-md min-w-0">...</div>
```

Named `max-w-*` sizes: `max-w-xs` (20rem), `max-w-sm` (24rem), `max-w-md` (28rem), `max-w-lg` (32rem), `max-w-xl` (36rem), up to `max-w-7xl` (80rem), plus `max-w-prose` (about 65 characters, good for text) and `max-w-full`.

**`size-*` sets width and height together**, which is handy for squares and circles:

```html
<div class="size-12 rounded-full bg-indigo-500"></div>
```

**Aspect ratio**

```html
<div class="aspect-video w-full bg-gray-200">16:9</div>
<div class="aspect-square w-32 bg-gray-200">1:1</div>
```

**Tip:** `min-h-screen` is usually better than `h-screen` for page wrappers. The page can grow if content is taller than the screen, instead of overflowing.

---

## 4.3 Borders and radius

**Border width**

| Class | Effect |
|---|---|
| `border` | 1px on all sides |
| `border-2`, `border-4`, `border-8` | Thicker |
| `border-t`, `border-r`, `border-b`, `border-l` | One side (`border-t-2` for thickness) |
| `border-x`, `border-y` | Two sides |
| `border-0` | None |

**Border color and style**

```html
<div class="border border-gray-300">Light gray border</div>
<div class="border-2 border-dashed border-blue-500">Dashed</div>
```

Always set a color. In v4, the default border color is `currentColor` (the text color), whereas v3 used light gray.

**Border radius**

| Class | Size |
|---|---|
| `rounded-xs` | 0.125rem |
| `rounded-sm` | 0.25rem |
| `rounded-md` | 0.375rem |
| `rounded-lg` | 0.5rem |
| `rounded-xl` | 0.75rem |
| `rounded-2xl` | 1rem |
| `rounded-full` | Pill or circle |
| `rounded-none` | Square |

Round one corner or side with `rounded-t-lg`, `rounded-bl-xl`, and so on.

(In v3 the names were shifted: `rounded-sm` was 0.125rem and plain `rounded` was 0.25rem. v4 renamed them to `rounded-xs` and `rounded-sm`.)

**Example: a card**

```html
<div class="rounded-xl border border-gray-200 bg-white p-6">
  <h3 class="font-semibold">Card title</h3>
</div>
```

**Example: an avatar**

```html
<img src="me.jpg" alt="Profile photo" class="size-16 rounded-full border-2 border-white" />
```

---

## 4.4 Divide and ring utilities

**Divide** adds borders *between* children without styling each one.

```html
<ul class="divide-y divide-gray-200 rounded-lg border border-gray-200">
  <li class="p-4">First</li>
  <li class="p-4">Second</li>
  <li class="p-4">Third</li>
</ul>
```

- `divide-y` puts horizontal lines between stacked items. `divide-x` puts vertical lines between side-by-side items.
- `divide-gray-200` sets the color, and `divide-dashed` sets the style.

**Ring** draws an outline-like border using `box-shadow`, which does not change layout size. It is commonly used for focus states.

```html
<button class="rounded-md bg-white px-4 py-2 ring-1 ring-gray-300 focus:ring-2 focus:ring-blue-500">
  Click
</button>
```

| Class | Meaning |
|---|---|
| `ring` | 1px ring (v4). In v3, plain `ring` was 3px, so use `ring-3` for the old look |
| `ring-2`, `ring-4` | Thicker rings |
| `ring-blue-500` | Ring color |
| `ring-inset` | Draw the ring inside the element |
| `ring-offset-2` | Gap between the element and the ring |

**Outline utilities** (`outline-2`, `outline-offset-2`, `outline-blue-600`) are the alternative for focus indication, and they follow border radius in modern browsers. Lesson 16 shows the recommended focus pattern.

---

[Previous](./[3]-Layout.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[5]-Typography-And-Colors.md)