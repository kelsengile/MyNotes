[Previous](./[16]-Testing-Vue-Applications.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[18]-Building-And-Deployment.md)

*Ecosystem And Delivery*

# Lesson 17 - Performance Optimization

## 17.1 Reactivity pitfalls

Vue is fast by default. Measure first with the browser's performance tools and the Vue DevTools, then optimize only the real bottlenecks.

**Pitfall 1: deep reactivity on large data.** `ref` and `reactive` convert every nested property into a reactive one. For large objects or big arrays that you only replace as a whole, use `shallowRef`:

```js
import { shallowRef } from 'vue'

const bigList = shallowRef([])    // only .value replacement is tracked

// ✅ triggers an update
bigList.value = newArray

// ❌ does NOT trigger an update (deep changes are not tracked)
bigList.value.push(item)
```

**Pitfall 2: expensive work in templates.** Template expressions rerun on every render. Move heavy work to a `computed` (which is cached):

```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([/* thousands of items */])
const search = ref('')

// ✅ calculated once, then cached until items or search changes
const filtered = computed(() =>
  items.value.filter(i => i.name.includes(search.value))
)
</script>

<template>
  <!-- ❌ avoid: filters again on every render -->
  <!-- <li v-for="i in items.filter(x => x.name.includes(search))"> -->

  <li v-for="i in filtered" :key="i.id">{{ i.name }}</li>
</template>
```

**Pitfall 3: passing too much to child components.** A child re-renders when any prop it uses changes. Pass stable, minimal props rather than whole large objects.

**Pitfall 4: always missing `:key` or using index keys** in changing lists, which leads to unnecessary DOM work and bugs.

**Helpful tools:**

- `v-once`: render an element once and never update it.
- `v-memo="[a, b]"`: skip re-rendering a subtree unless the listed values change (useful for huge lists).
- `markRaw(obj)`: prevent Vue from making an object reactive (for third-party class instances such as a map or chart object).

---

## 17.2 Lazy loading components

**Lazy loading** delays downloading code until it is needed, which makes the first page load faster.

Use `defineAsyncComponent` for components:

```vue
<script setup>
import { ref, defineAsyncComponent } from 'vue'

const HeavyChart = defineAsyncComponent(() => import('./HeavyChart.vue'))

const showChart = ref(false)
</script>

<template>
  <button @click="showChart = true">Show chart</button>
  <HeavyChart v-if="showChart" />   <!-- downloaded only when first shown -->
</template>
```

With loading and error states:

```js
const HeavyChart = defineAsyncComponent({
  loader: () => import('./HeavyChart.vue'),
  loadingComponent: LoadingSpinner,
  errorComponent: ErrorMessage,
  delay: 200,       // wait 200 ms before showing the loading component
  timeout: 10000    // fail after 10 s
})
```

Good candidates for lazy loading:

- Routes (see Lesson 10)
- Modals and dialogs that open on demand
- Large third-party widgets (charts, editors, maps)
- Components below the fold, or in tabs that are not visible at first

Other loading improvements:

- Use modern image formats (WebP, AVIF) and the `loading="lazy"` attribute on `<img>` tags.
- Split large libraries and import only what you need (`import { debounce } from 'lodash-es'` instead of the whole library).
- Wrap tabbed content in `<KeepAlive>` to **cache** component state instead of re-creating it when switching tabs.

---

## 17.3 Virtual scrolling

Rendering thousands of DOM elements makes pages slow and memory-heavy. **Virtual scrolling** (windowing) renders only the items currently visible, plus a small buffer.

```
All 100,000 items in data
┌──────────────┐
│  (not in DOM)│
├──────────────┤
│  visible 1   │ ◄── only these ~20 rows exist in the DOM
│  visible 2   │
│     ...      │
├──────────────┤
│  (not in DOM)│
└──────────────┘
```

Libraries that do this for you:

| Library | Notes |
|---|---|
| `vue-virtual-scroller` | Popular, simple components |
| `@tanstack/vue-virtual` | Headless, flexible |
| `vueuc` / UI libraries | Often include virtual lists or tables |

Example with `vue-virtual-scroller` (install with `npm install vue-virtual-scroller`, and check its README for the version matching Vue 3):

```vue
<script setup>
import { RecycleScroller } from 'vue-virtual-scroller'
import 'vue-virtual-scroller/dist/vue-virtual-scroller.css'

const items = Array.from({ length: 100000 }, (_, i) => ({
  id: i,
  name: `Item ${i}`
}))
</script>

<template>
  <RecycleScroller
    class="scroller"
    :items="items"
    :item-size="32"
    key-field="id"
    v-slot="{ item }"
  >
    <div class="row">{{ item.name }}</div>
  </RecycleScroller>
</template>

<style scoped>
.scroller {
  height: 400px;   /* a fixed height is required */
}
.row {
  height: 32px;
}
</style>
```

When virtual scrolling is **not** needed: lists under a few hundred simple items. Alternatives for large data are **pagination** or "load more" buttons, which are often simpler and more accessible.

---

## 17.4 Build analysis

Bundle size directly affects load time. To improve it, you first need to see **what is in the bundle**.

**Step 1: build and read the output.**

```bash
npm run build
```

Vite lists each generated file with its size, including gzip size:

```
dist/assets/index-4f3a2b1c.js    142.30 kB │ gzip: 46.12 kB
```

**Step 2: visualize the contents** with `rollup-plugin-visualizer`:

```bash
npm install -D rollup-plugin-visualizer
```

```js
// vite.config.js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import { visualizer } from 'rollup-plugin-visualizer'

export default defineConfig({
  plugins: [
    vue(),
    visualizer({ open: true })   // opens a treemap after build
  ]
})
```

Run `npm run build` again. A chart opens in your browser showing which packages take up the most space.

**Step 3: act on what you find.**

| Finding | Possible fix |
|---|---|
| One huge library | Replace with a lighter alternative (for example, `date-fns` or `dayjs` instead of `moment`) |
| Code not needed at startup | Lazy load it (routes and async components) |
| Duplicated dependencies | Align versions, run `npm dedupe` |
| Large images | Compress and use modern formats |
| Unused code | Use named imports so **tree-shaking** can remove it |

Also use these measurement tools:

- **Lighthouse** (in Chrome DevTools) reports loading performance and Core Web Vitals.
- **Vue DevTools** shows component render timing in its performance tab.

Remember the order: **measure, change one thing, measure again**.

---

[Previous](./[16]-Testing-Vue-Applications.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[18]-Building-And-Deployment.md)