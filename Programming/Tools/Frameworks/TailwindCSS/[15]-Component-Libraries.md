[Previous](./[14]-Using-Tailwind-With-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[16]-Accessibility-And-Best-Practices.md)

*Practical Use*

# Lesson 15 - Component Libraries

## 15.1 shadcn/ui

**shadcn/ui** is not a traditional package. It is a collection of well-designed, accessible components that you **copy into your own project**. After that, the code is yours to edit.

**How it is built:** React components using Tailwind for styling and Radix UI primitives for behavior and accessibility.

**Setup**

```bash
npx shadcn@latest init
```

The wizard configures Tailwind, a `cn` helper, and your theme variables. Then add components one at a time:

```bash
npx shadcn@latest add button
npx shadcn@latest add dialog card input
```

Each command creates a file such as `components/ui/button.tsx` in your project.

**Usage**

```jsx
import { Button } from "@/components/ui/button";

export default function Page() {
  return (
    <div className="flex gap-3">
      <Button>Default</Button>
      <Button variant="outline">Outline</Button>
      <Button variant="destructive" size="sm">Delete</Button>
    </div>
  );
}
```

**Customizing is just editing.** Open `components/ui/button.tsx`, and you will see Tailwind classes and a `cva` variants definition (Lesson 14). Change them directly.

**Theming with CSS variables.** shadcn/ui uses the semantic token system from Lesson 9.4 (`bg-background`, `text-foreground`, `bg-primary`, `border-border`). To re-theme, edit the variables in your global CSS and both light and dark modes update.

**Strengths and trade-offs**

| Strengths | Trade-offs |
|---|---|
| Full ownership and control | You maintain the copied code |
| Accessible by default | React-focused (community ports exist for Vue and Svelte) |
| Looks great and is easy to restyle | Updates are manual, since you re-add or merge changes |

---

## 15.2 Headless UI

**Headless UI** is a set of fully **unstyled**, accessible components made by the Tailwind team. They provide behavior (keyboard navigation, focus management, ARIA attributes) and you provide every style with Tailwind classes.

**Available for React and Vue.**

```bash
npm install @headlessui/react
```

**Components include:** Dialog (modal), Menu (dropdown), Listbox (custom select), Combobox (autocomplete), Disclosure (accordion), Popover, Switch, Tabs, Radio Group, Transition.

**Example: a disclosure (expand and collapse)**

```jsx
import { Disclosure, DisclosureButton, DisclosurePanel } from "@headlessui/react";

export default function Faq() {
  return (
    <Disclosure>
      <DisclosureButton className="w-full rounded-md bg-gray-100 px-4 py-2 text-left font-medium data-open:bg-gray-200">
        What is Tailwind CSS?
      </DisclosureButton>
      <DisclosurePanel className="px-4 py-3 text-gray-600">
        A utility-first CSS framework.
      </DisclosurePanel>
    </Disclosure>
  );
}
```

**How state styling works.** Headless UI adds data attributes such as `data-open`, `data-focus`, `data-active`, `data-checked`, and `data-disabled`. You style them with Tailwind's data variants: `data-open:bg-gray-200`, `data-focus:ring-2`, `data-checked:bg-blue-600`.

**When to choose Headless UI**

- You have a custom design and want complete control over the look.
- You need correct accessibility for complex widgets (dialogs, comboboxes) without writing it yourself.

**Alternatives with the same idea:** Radix UI, React Aria Components, and Ark UI.

---

## 15.3 daisyUI and Flowbite

These libraries give you **pre-styled component classes** so you write far fewer utilities.

**daisyUI** is a Tailwind plugin that adds semantic component class names.

```bash
npm install -D daisyui@latest
```

```css
@import "tailwindcss";
@plugin "daisyui";
```

```html
<button class="btn btn-primary">Primary</button>
<div class="card bg-base-100 w-96 shadow-sm">
  <div class="card-body">
    <h2 class="card-title">Card title</h2>
    <p>Content goes here.</p>
    <div class="card-actions justify-end">
      <button class="btn btn-secondary">Buy now</button>
    </div>
  </div>
</div>
```

- **Framework-agnostic**, since it is just CSS classes.
- **Built-in themes** (light, dark, cupcake, and many more), switched with `data-theme="dark"` on the `<html>` tag.
- You can still add Tailwind utilities to override anything.

**Flowbite** provides a large set of ready-made components and sections, with optional JavaScript for interactive items like modals and dropdowns. Its plugin is added in the same `@plugin` style, and the setup steps change between versions, so follow its current installation guide.

```html
<!-- Flowbite-style markup is built from plain utilities -->
<button type="button" class="rounded-lg bg-blue-700 px-5 py-2.5 text-sm font-medium text-white hover:bg-blue-800 focus:ring-4 focus:ring-blue-300">
  Default
</button>
```

**Other options:** Preline, HyperUI (free copy-paste snippets), Tailwind Plus (the official paid collection of UI blocks, formerly Tailwind UI), Park UI, and Mantine-style kits for specific frameworks.

---

## 15.4 Choosing a library

There is no single best choice. Match the tool to your project.

| Library | Type | Best when | Watch out for |
|---|---|---|---|
| **None (own components)** | Hand-built | Unique brand, small app, learning | Accessibility for complex widgets takes real work |
| **shadcn/ui** | Copy-in React components | React apps needing quality and full control | You own maintenance |
| **Headless UI / Radix** | Unstyled behavior | Fully custom designs with correct accessibility | You write all styling |
| **daisyUI** | CSS component classes | Fast prototypes, any framework | Sites can look similar without theming |
| **Flowbite** | Components plus JS | Dashboards and admin panels quickly | Extra JavaScript and setup per version |
| **Tailwind Plus** | Paid UI blocks | Professional layouts to adapt | Cost, and you adapt the code |

**Questions to ask**

1. **Framework:** does it support the one you use (React, Vue, Svelte, plain HTML)?
2. **Customization:** how easily can you match your brand?
3. **Accessibility:** are keyboard and screen reader support built in?
4. **Ownership:** do you want a dependency you update, or code you own?
5. **Maintenance and size:** is it actively maintained, and how much does it add to your bundle?
6. **Learning goals:** if you are learning Tailwind, build a few components by hand first, since libraries hide the utilities you want to learn.

**A reasonable path:** start with plain Tailwind, add Headless UI or Radix for complex interactive pieces, and adopt shadcn/ui when you want a full, customizable component set in React.

---

[Previous](./[14]-Using-Tailwind-With-Frameworks.md) | [Table of Contents](./[0]-Introduction-to-TailwindCSS.md) | [Next](./[16]-Accessibility-And-Best-Practices.md)