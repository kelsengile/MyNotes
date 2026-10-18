[Previous](./[1]-What-Is-Tailwind-CSS.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[3]-Layout.md)

*Basics*

# Lesson 2 - Utility-First Fundamentals

## 2.1 Composing classes

Everything in Tailwind is **composition**: you combine small classes to describe a design. Think of each class as one sentence in a description of the element.

```html
<div class="mx-auto max-w-sm rounded-xl bg-white p-6 shadow-lg">
  <h2 class="text-xl font-semibold text-gray-900">Notifications</h2>
  <p class="mt-2 text-gray-600">You have 3 unread messages.</p>
  <button class="mt-4 rounded-md bg-blue-600 px-3 py-1.5 text-sm font-medium text-white">
    View all
  </button>
</div>
```

**A helpful mental model: group classes by purpose**

| Group | Examples |
|---|---|
| Layout | `flex`, `grid`, `relative`, `hidden` |
| Spacing and size | `p-4`, `mt-2`, `w-full`, `max-w-sm` |
| Typography | `text-xl`, `font-semibold`, `leading-6` |
| Colors and visuals | `bg-white`, `text-gray-600`, `shadow-lg`, `rounded-xl` |
| States | `hover:bg-blue-700`, `focus:ring-2` |
| Responsive | `md:flex`, `lg:grid-cols-3` |

**Class order in HTML does not matter.** `p-4 text-white` and `text-white p-4` behave identically. The generated stylesheet decides which rule wins, not the order you typed the classes.

**Naming pattern:** most utilities follow `property-value`: `text-center`, `bg-red-500`, `w-64`. Once you learn the pattern, you can often guess a class name correctly.

---

## 2.2 Readability trade-offs

The most common complaint about Tailwind is long class lists. Here is an honest look at both sides.

**Costs**

- Markup becomes visually busy.
- The same group of classes can be repeated across elements.
- New teammates must learn the utility vocabulary.

**Benefits**

- You can see an element's styling without opening another file.
- Deleting an element deletes its styles, so there is no dead CSS.
- You rarely need to scroll between HTML and CSS.

**How to keep things readable**

1. **Extract components.** If a button appears ten times, make a `<Button>` component (Lesson 12) instead of copying classes.
2. **Use multi-cursor editing.** When classes repeat inside one file (a list of items), edit them all at once.
3. **Sort and format classes automatically** with the Prettier plugin (Lesson 17).
4. **Break long lines.** Put one group of classes per line in your template if your formatter allows it.
5. **Do not abstract too early.** Duplication inside one component is fine. Abstract when you repeat across files.

```html
<!-- Hard to scan -->
<button class="inline-flex items-center gap-2 rounded-md bg-indigo-600 px-4 py-2 text-sm font-semibold text-white shadow-sm transition hover:bg-indigo-500 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">

<!-- Same classes, easier to scan in frameworks that allow multi-line attributes -->
<button
  class="
    inline-flex items-center gap-2
    rounded-md bg-indigo-600 px-4 py-2 shadow-sm
    text-sm font-semibold text-white
    transition hover:bg-indigo-500
    focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600
  "
>
```

---

## 2.3 Arbitrary values

Tailwind's scale covers most needs, but sometimes a design needs an exact one-off value. Use **square brackets** to write any value directly.

```html
<div class="w-[320px] h-[3.5rem] top-[117px] bg-[#1da1f2] text-[22px]"></div>
```

**Rules and tips**

- **Spaces become underscores.** CSS values with spaces use `_` instead.

```html
<div class="grid grid-cols-[1fr_2fr_1fr]"></div>
```

- **Commas are fine:**

```html
<div class="grid-cols-[repeat(auto-fit,minmax(16rem,1fr))]"></div>
```

- **Arbitrary properties** let you use any CSS property that has no utility, written as `[property:value]`:

```html
<div class="[mask-type:luminance] [text-wrap:balance]"></div>
```

- **CSS variables** use parentheses in v4:

```html
<div class="bg-(--brand-color) w-(--sidebar-width)"></div>
```

(In v3 the syntax was `bg-[--brand-color]`.)

- **Type hints** disambiguate when a value could mean more than one thing. For example, `text-[length:var(--size)]` is a font size, while `text-[color:var(--c)]` is a color.

- **Arbitrary variants** write a custom selector or condition:

```html
<ul class="[&>li]:mb-2 [&_a]:underline"></ul>
<div class="min-[900px]:flex"></div>
```

**Guideline:** if you use the same arbitrary value more than twice, add it to your theme instead (Lesson 11).

---

## 2.4 Important modifier and conflicts

**Conflicts.** When two utilities set the *same CSS property* on one element, which one wins is decided by their order in Tailwind's generated stylesheet, **not** by their order in your `class` attribute.

```html
<!-- Unpredictable: both set padding -->
<div class="p-2 p-8"></div>
```

Avoid this by never putting competing utilities on the same element. When classes are built dynamically in components, use a merge helper such as `tailwind-merge` (Lesson 14).

**The important modifier.** To force a utility to win regardless of order, add `!` at the **end** of the class (v4 syntax):

```html
<p class="text-gray-500 text-red-600!">This is red</p>
```

In v4, a leading `!` (`!text-red-600`) still works for compatibility, but the trailing form is preferred.

This generates `color: ... !important`. Use it rarely, mainly when you have to override third-party CSS you cannot change.

**Make everything important (rare):**

```css
@import "tailwindcss" important;
```

**Prefixing.** If Tailwind classes collide with another library's class names, add a prefix:

```css
@import "tailwindcss" prefix(tw);
```

```html
<div class="tw:flex tw:p-4"></div>
```

**Summary of the safest habits**

1. One utility per CSS property per element.
2. Use state and responsive variants instead of competing classes.
3. Reach for `!` only as a last resort.

---

[Previous](./[1]-What-Is-Tailwind-CSS.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[3]-Layout.md)