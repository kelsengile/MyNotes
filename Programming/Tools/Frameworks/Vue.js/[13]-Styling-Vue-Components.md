[Previous](./[12]-Data-Fetching.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[14]-TypeScript-With-Vue.md)

*Building Applications*

# Lesson 13 - Styling Vue Components

## 13.1 Scoped styles

CSS is global by default, so a rule like `button { color: red }` in one component affects every button in the app. The `scoped` attribute limits styles to the **current component** only.

```vue
<template>
  <button class="btn">Click</button>
</template>

<style scoped>
.btn {
  background: seagreen;
  color: white;
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 6px;
}
</style>
```

Vue adds a unique attribute (like `data-v-7ba5bd90`) to the component's elements and rewrites the CSS to match it:

```css
.btn[data-v-7ba5bd90] { ... }
```

Things to know:

- A scoped style affects the component's own elements and the **root element** of child components, but not deeper child elements.
- To style inside a child component, use the `:deep()` selector:

```vue
<style scoped>
.wrapper :deep(.child-class) {
  color: red;
}
</style>
```

- Other pseudo-selectors: `:slotted(.x)` styles slot content, and `:global(.x)` creates a global rule from inside a scoped block.
- You can mix both in one file: use a normal `<style>` for global rules (resets, fonts) and `<style scoped>` for component rules.
- Prefer **class selectors** over element selectors in scoped CSS for better performance.

---

## 13.2 CSS Modules

**CSS Modules** generate unique class names to avoid conflicts, and expose them as a JavaScript object. Add the `module` attribute:

```vue
<template>
  <p :class="$style.red">This is red</p>
  <p :class="[$style.red, $style.bold]">Red and bold</p>
</template>

<style module>
.red {
  color: red;
}
.bold {
  font-weight: bold;
}
</style>
```

`$style` is available in the template automatically. In `<script setup>`, use `useCssModule()`:

```vue
<script setup>
import { useCssModule } from 'vue'

const style = useCssModule()
console.log(style.red)   // e.g. "_red_1x2y3"
</script>
```

You can give the module a custom name:

```vue
<template>
  <p :class="classes.title">Hello</p>
</template>

<style module="classes">
.title {
  font-size: 2rem;
}
</style>
```

| | Scoped | CSS Modules |
|---|---|---|
| How it isolates | Attribute selectors | Unique class names |
| Usage in template | Normal `class="btn"` | `:class="$style.btn"` |
| Access from JS | No | Yes |

Both are good choices. `scoped` is more common because it is simpler to write.

---

## 13.3 Using Tailwind CSS

**Tailwind CSS** is a utility-first framework. Instead of writing custom CSS, you apply small classes directly in the template.

Installation with Vite (Tailwind v4):

```bash
npm install tailwindcss @tailwindcss/vite
```

Add the plugin in `vite.config.js`:

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [vue(), tailwindcss()]
})
```

Import Tailwind in your main CSS file (for example `src/assets/main.css`):

```css
@import "tailwindcss";
```

Make sure that CSS file is imported in `main.js`:

```js
import './assets/main.css'
```

Use utility classes in a component:

```vue
<template>
  <button class="rounded-lg bg-emerald-600 px-4 py-2 font-semibold text-white hover:bg-emerald-700">
    Save
  </button>
</template>
```

Combine Tailwind with dynamic classes:

```vue
<script setup>
defineProps({ active: Boolean })
</script>

<template>
  <button :class="['px-4 py-2 rounded', active ? 'bg-blue-600 text-white' : 'bg-gray-200']">
    Tab
  </button>
</template>
```

Tailwind's setup steps differ between major versions, so confirm the current instructions at [tailwindcss.com](https://tailwindcss.com). Pros: fast to write, consistent design. Cons: long class lists in templates. Extract repeated patterns into components rather than copying class strings.

---

## 13.4 Dynamic styling with v-bind

There are two ways to make styles react to data.

**In the template**, bind `class` and `style`:

```vue
<script setup>
import { ref } from 'vue'

const isActive = ref(true)
const hasError = ref(false)
const fontSize = ref(18)
</script>

<template>
  <!-- object syntax -->
  <div :class="{ active: isActive, 'text-danger': hasError }">Object</div>

  <!-- array syntax -->
  <div :class="[isActive ? 'active' : '', 'always']">Array</div>

  <!-- inline style -->
  <div :style="{ fontSize: fontSize + 'px', color: 'steelblue' }">Styled</div>
</template>
```

**In the `<style>` block**, use `v-bind()` to link a CSS value to a reactive variable:

```vue
<script setup>
import { ref } from 'vue'

const color = ref('tomato')
const size = ref(24)
</script>

<template>
  <p class="text">Dynamic CSS</p>
  <input type="color" v-model="color" />
  <input type="range" min="12" max="60" v-model.number="size" />
</template>

<style scoped>
.text {
  color: v-bind(color);
  font-size: v-bind('size + "px"');   /* wrap JS expressions in quotes */
}
</style>
```

Vue turns these into CSS custom properties, so the styles update instantly when the variable changes.

Use `v-bind()` in CSS when a value is truly dynamic (user-chosen colors, sizes). For simple on/off states, toggling a CSS class is easier to read and maintain.

---

[Previous](./[12]-Data-Fetching.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[14]-TypeScript-With-Vue.md)