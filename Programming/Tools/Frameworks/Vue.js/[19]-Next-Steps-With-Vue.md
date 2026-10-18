[Previous](./[18]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md)

*Ecosystem And Delivery*

# Lesson 19 - Next Steps With Vue

## 19.1 UI libraries like Vuetify and PrimeVue

A **UI component library** gives you ready-made, accessible, styled components (buttons, tables, dialogs, date pickers) so you do not build everything from scratch.

| Library | Style | Notes |
|---|---|---|
| **Vuetify** | Material Design | Very complete, large component set |
| **PrimeVue** | Flexible themes | Rich data tables, many components, supports "unstyled" mode |
| **Element Plus** | Clean, business-oriented | Popular for admin dashboards |
| **Naive UI** | Modern, TypeScript-first | Good theming support |
| **Quasar** | Material-based framework | Targets web, mobile, and desktop from one codebase |
| **Nuxt UI / Radix Vue / shadcn-vue** | Headless or Tailwind-based | Accessible building blocks you style yourself |

Example using Vuetify (install with `npm install vuetify`, then follow its setup guide for your version):

```js
// main.js
import { createApp } from 'vue'
import { createVuetify } from 'vuetify'
import * as components from 'vuetify/components'
import * as directives from 'vuetify/directives'
import 'vuetify/styles'
import App from './App.vue'

const vuetify = createVuetify({ components, directives })

createApp(App).use(vuetify).mount('#app')
```

```vue
<template>
  <v-app>
    <v-btn color="primary">Click me</v-btn>
  </v-app>
</template>
```

Choosing a library:

- **Need speed and consistency?** Pick a full-featured library (Vuetify, PrimeVue, Element Plus).
- **Need a unique brand look?** Use headless components with your own styling (Tailwind).
- Check that it supports **Vue 3**, has active maintenance, and offers accessibility (keyboard navigation, ARIA attributes).
- Importing only what you use (tree-shaking) keeps bundles small.

Install and setup steps change between versions, so follow each library's official documentation.

---

## 19.2 Advanced patterns

Once you are comfortable with the basics, these techniques will deepen your skills.

**Render functions and JSX** build UI with JavaScript instead of templates, for highly dynamic output:

```js
import { h } from 'vue'

export default {
  props: ['level'],
  setup(props, { slots }) {
    return () => h(`h${props.level}`, {}, slots.default?.())
  }
}
```

**Custom plugins and typed injection** (see Lessons 9 and 14) for shared services.

**Renderless components** hold logic and expose it through a scoped slot, leaving the markup to the parent:

```vue
<!-- MouseTracker.vue -->
<script setup>
import { useMouse } from '@/composables/useMouse'

const { x, y } = useMouse()
</script>

<template>
  <slot :x="x" :y="y" />
</template>
```

Today, most renderless logic is written as composables instead.

**Other patterns worth learning:**

| Pattern | Idea |
|---|---|
| `<KeepAlive>` | Cache component instances when switching |
| `defineExpose` / template refs | Controlled parent-to-child communication |
| Provide/inject with typed keys | Safe dependency injection |
| `v-model` with custom modifiers | Advanced component APIs |
| Error boundaries with `onErrorCaptured` | Catch and display child errors gracefully |
| Component libraries and monorepos | Share components between projects |
| Accessibility (a11y) | Semantic HTML, focus management, ARIA, tested with screen readers |

Good habits to keep practicing:

- Keep components **small and focused**.
- **Lift state up** only as far as needed.
- Prefer **composition** (composables and slots) over complicated inheritance.
- Write **tests** for important behavior.

---

## 19.3 Vue 3 internals

Understanding how Vue works internally helps you debug and reason about performance.

**1. Reactivity system.** Vue wraps data in JavaScript `Proxy` objects. Reading a property inside a render function or `computed` records a dependency. Writing to it notifies exactly the effects that depend on it (see Lesson 3).

You can build a tiny version yourself:

```js
let activeEffect = null

function effect(fn) {
  activeEffect = fn
  fn()
  activeEffect = null
}

function reactive(target) {
  const deps = {}
  return new Proxy(target, {
    get(obj, key) {
      if (activeEffect) {
        (deps[key] ||= new Set()).add(activeEffect)   // track
      }
      return obj[key]
    },
    set(obj, key, value) {
      obj[key] = value
      deps[key]?.forEach(fn => fn())                  // trigger
      return true
    }
  })
}

const state = reactive({ count: 0 })
effect(() => console.log('Count is', state.count))   // Count is 0
state.count++                                         // Count is 1
```

**2. Template compiler.** Templates are compiled into JavaScript **render functions** at build time. The compiler analyzes the template and marks which parts are static and which are dynamic.

**3. Virtual DOM.** A render function returns a tree of lightweight JavaScript objects ("virtual nodes"). When state changes, Vue creates a new tree, compares it to the old one (**diffing**), and applies only the differences to the real DOM (**patching**).

**4. Compiler-informed optimizations.** Because the compiler knows the template structure, Vue 3 can:

- Hoist static content so it is created once.
- Add **patch flags** to dynamic nodes, so Vue skips comparing everything that cannot change.
- Cache event handlers.

**5. Scheduler.** Updates are queued and flushed asynchronously in one batch (which is why `nextTick` exists).

**Where to explore further:** the Vue source code on GitHub (`vuejs/core`), and the official guide section on rendering mechanism and reactivity in depth.

---

## 19.4 Project ideas

The best way to learn is to build. Start small and add features gradually. Each idea lists the Vue skills it practices.

**Beginner**

| Project | Skills practiced |
|---|---|
| **To-do list** | `v-for`, `v-model`, events, computed (filter done/active) |
| **Counter with history** | `ref`, watchers, lists |
| **Tip calculator** | Forms, computed |
| **Quiz app** | Components, props, emits, state |

**Intermediate**

| Project | Skills practiced |
|---|---|
| **Weather app** | Fetching APIs, loading and error states, composables |
| **Notes app with search** | Pinia, `localStorage` persistence, computed filtering |
| **Movie browser** | Vue Router (list and detail pages), API fetching, lazy loading |
| **Expense tracker** | Forms and validation, Pinia, charts |
| **Kanban board** | Drag and drop, nested components, state management |

**Advanced**

| Project | Skills practiced |
|---|---|
| **Blog with Nuxt** | File-based routing, SSR/SSG, SEO, server routes |
| **E-commerce store** | Router guards, cart store, TypeScript, testing |
| **Dashboard with authentication** | Guards, Vue Query, charts, virtual scrolling |
| **Component library** | Reusable components, TypeScript, documentation, publishing to npm |
| **Real-time chat** | WebSockets, composables, Pinia, deployment |

Tips for finishing projects:

1. **Plan** the screens and components on paper first.
2. Build the **smallest working version** before adding extras.
3. Add **TypeScript and tests** once the idea works.
4. **Deploy** it (Lesson 18) and share the link.
5. Keep your code in a **public repository** with a clear README.

Keep learning with the official documentation at [vuejs.org](https://vuejs.org), the Vue community on Discord, and open-source projects you can read and contribute to. Happy building!

---

[Previous](./[18]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md)