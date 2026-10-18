[Previous](./[6]-Backgrounds-And-Effects.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[8]-Responsive-Design.md)

*Responsive And Interactive Design*

# Lesson 7 - State Variants

## 7.1 Hover and focus

A **variant** is a prefix that makes a utility apply only under a condition. The format is `variant:utility`.

```html
<button class="bg-blue-600 text-white hover:bg-blue-700 focus:ring-2 focus:ring-blue-300">
  Save
</button>
```

**Common interaction variants**

| Variant | Applies when |
|---|---|
| `hover:` | The pointer is over the element |
| `focus:` | The element has focus (click or keyboard) |
| `focus-visible:` | Focused **and** the browser decides a focus ring should show (typically keyboard use) |
| `focus-within:` | The element or any child has focus |

**`focus` vs `focus-visible`.** `focus:` also triggers when you click a button with the mouse, which can look noisy. `focus-visible:` is usually the better choice for rings and outlines because it targets keyboard users.

```html
<a href="#" class="rounded px-2 py-1 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-blue-600">
  Link
</a>
```

**Smooth the change with a transition:**

```html
<button class="bg-gray-800 text-white transition-colors hover:bg-gray-600">Smooth hover</button>
```

**Example: a form field that highlights its whole wrapper**

```html
<div class="rounded-lg border border-gray-300 p-2 focus-within:border-blue-500 focus-within:ring-2 focus-within:ring-blue-200">
  <input class="w-full outline-hidden" placeholder="Email" />
</div>
```

**Note on touch devices.** In v4, `hover:` styles only apply on devices that actually support hover, so phones do not get "stuck" hover states after a tap.

---

## 7.2 Active and disabled

**More state variants**

| Variant | Applies when |
|---|---|
| `active:` | The element is being pressed |
| `disabled:` | A form control is disabled |
| `enabled:` | A form control is not disabled |
| `checked:` | A checkbox or radio is checked |
| `invalid:` | Form validation fails |
| `required:`, `optional:` | Input has or lacks `required` |
| `placeholder-shown:` | Placeholder is visible |
| `visited:` | Link has been visited |
| `first:`, `last:`, `odd:`, `even:` | Position among siblings |
| `empty:` | Element has no children |

**Example: a button with all states**

```html
<button
  class="rounded-lg bg-indigo-600 px-4 py-2 font-medium text-white
         hover:bg-indigo-500
         active:scale-95
         disabled:cursor-not-allowed disabled:opacity-50 disabled:hover:bg-indigo-600"
  disabled
>
  Submit
</button>
```

**Example: striped rows**

```html
<tr class="odd:bg-white even:bg-gray-50">...</tr>
```

**Example: validation styling**

```html
<input
  type="email"
  required
  class="rounded border border-gray-300 px-3 py-2 invalid:border-red-500 invalid:text-red-600"
/>
```

**Attribute-based variants**

Use `aria-*` and `data-*` variants to style based on attributes. This works well with component libraries that set these attributes (Lesson 15).

```html
<button aria-expanded="true" class="aria-expanded:bg-gray-200">Menu</button>
<div data-state="open" class="data-[state=open]:opacity-100 opacity-0">Panel</div>
<div data-active class="data-active:font-bold">Boolean data attribute (v4 shorthand)</div>
```

**Stacking variants.** Variants combine from left to right:

```html
<button class="bg-blue-600 md:hover:bg-blue-700 dark:disabled:opacity-40">...</button>
```

Read it as "on md screens and larger, when hovered".

---

## 7.3 Group and peer

Sometimes an element needs to react to the state of a *different* element.

**`group`: style children based on a parent's state**

Add `group` to the parent, then `group-hover:`, `group-focus:`, and so on to children.

```html
<a href="#" class="group block rounded-lg border p-4 hover:border-blue-500">
  <h3 class="font-semibold text-gray-900 group-hover:text-blue-600">Card title</h3>
  <p class="text-gray-500 group-hover:text-gray-700">Hover anywhere on the card.</p>
  <span class="inline-block transition-transform group-hover:translate-x-1">→</span>
</a>
```

**Named groups** avoid confusion when groups nest:

```html
<div class="group/card">
  <div class="group/item">
    <span class="group-hover/card:text-red-500 group-hover/item:text-blue-500">Text</span>
  </div>
</div>
```

**`peer`: style an element based on a *previous sibling's* state**

Add `peer` to the earlier sibling, then `peer-checked:`, `peer-invalid:`, `peer-focus:` to a later sibling.

```html
<label class="flex items-center gap-3">
  <input type="checkbox" class="peer sr-only" />
  <span class="h-6 w-11 rounded-full bg-gray-300 transition peer-checked:bg-green-500"></span>
  <span class="text-gray-700 peer-checked:font-semibold">Notifications</span>
</label>
```

**The peer must come before the element being styled** in the HTML, because CSS sibling selectors only look forward.

**Example: an inline validation message**

```html
<input type="email" required class="peer rounded border px-3 py-2" />
<p class="hidden text-sm text-red-600 peer-invalid:block">Enter a valid email.</p>
```

**`has-*` variants** style a parent based on its descendants:

```html
<label class="rounded-lg border p-4 has-checked:border-blue-500 has-checked:bg-blue-50">
  <input type="radio" name="plan" /> Pro plan
</label>
```

---

## 7.4 Pseudo-elements

**`before:` and `after:`** generate decorative content. Tailwind adds `content: ""` automatically when you use them, so purely visual pseudo-elements need no extra class.

```html
<!-- A decorative underline bar -->
<h2 class="relative pb-2 after:absolute after:bottom-0 after:left-0 after:h-1 after:w-12 after:rounded after:bg-blue-500">
  Heading
</h2>
```

**Adding text content:**

```html
<span class="before:mr-1 before:content-['★'] before:text-yellow-500">Featured</span>
```

**Other pseudo-element variants**

| Variant | Targets |
|---|---|
| `placeholder:` | The placeholder text of an input |
| `file:` | The button inside `<input type="file">` |
| `marker:` | List bullets or numbers |
| `selection:` | Text the user highlights |
| `first-line:` | The first line of a paragraph |
| `first-letter:` | The first letter (drop caps) |
| `backdrop:` | The backdrop behind a `<dialog>` |

**Example: styled file input**

```html
<input
  type="file"
  class="text-sm text-gray-500
         file:mr-4 file:rounded-full file:border-0 file:bg-blue-50
         file:px-4 file:py-2 file:font-semibold file:text-blue-700
         hover:file:bg-blue-100"
/>
```

**Example: placeholder and selection styling**

```html
<input class="placeholder:italic placeholder:text-gray-400" placeholder="Search…" />
<p class="selection:bg-yellow-200 selection:text-black">Select some of this text.</p>
```

**Example: drop cap**

```html
<p class="first-letter:float-left first-letter:mr-2 first-letter:text-5xl first-letter:font-bold">
  Once upon a time…
</p>
```

---

[Previous](./[6]-Backgrounds-And-Effects.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[8]-Responsive-Design.md)