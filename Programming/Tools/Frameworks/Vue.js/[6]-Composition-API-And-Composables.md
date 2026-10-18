[Previous](./[5]-Events-And-Slots.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[7]-Lifecycle-Hooks.md)

*Components*

# Lesson 6 - Composition API And Composables

## 6.1 script setup

`<script setup>` is the recommended way to use the Composition API in single-file components. Code inside it runs each time a component instance is created.

```vue
<script setup>
import { ref, computed } from 'vue'
import MyChild from './MyChild.vue'

const count = ref(0)
const double = computed(() => count.value * 2)

function increment() {
  count.value++
}
</script>

<template>
  <MyChild />
  <button @click="increment">{{ count }} x 2 = {{ double }}</button>
</template>
```

What `<script setup>` gives you:

- **Top-level variables, functions, and imports** are automatically exposed to the template (no `return { ... }` needed).
- **Imported components** can be used directly.
- **Compiler macros** such as `defineProps`, `defineEmits`, and `defineModel` are available without importing.
- Less boilerplate and better performance than the older `setup()` function.

Compare with the older syntax, which needs an explicit return:

```vue
<script>
import { ref } from 'vue'

export default {
  setup() {
    const count = ref(0)
    return { count }   // must expose manually
  }
}
</script>
```

By default, a component using `<script setup>` is **closed**: a parent cannot access its internals through a template ref. Use `defineExpose` to share specific things:

```js
const open = ref(false)
defineExpose({ open })
```

---

## 6.2 Extracting logic into composables

A **composable** is a function that uses Vue's Composition API to package **reusable stateful logic**. Here is a counter composable, saved as `src/composables/useCounter.js`:

```js
import { ref, computed } from 'vue'

export function useCounter(initial = 0) {
  const count = ref(initial)
  const double = computed(() => count.value * 2)

  function increment() {
    count.value++
  }

  function reset() {
    count.value = initial
  }

  return { count, double, increment, reset }
}
```

Using it in any component:

```vue
<script setup>
import { useCounter } from '@/composables/useCounter'

const { count, double, increment, reset } = useCounter(10)
</script>

<template>
  <p>{{ count }} (double: {{ double }})</p>
  <button @click="increment">+1</button>
  <button @click="reset">Reset</button>
</template>
```

A composable can use any Vue feature: refs, computed, watchers, and lifecycle hooks. Here is a more realistic one that tracks the mouse and cleans up after itself:

```js
// src/composables/useMouse.js
import { ref, onMounted, onUnmounted } from 'vue'

export function useMouse() {
  const x = ref(0)
  const y = ref(0)

  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  return { x, y }
}
```

---

## 6.3 Reusing stateful logic

Every time a composable is called, it creates **fresh, independent state**. Two components that call `useCounter()` each get their own `count`:

```vue
<script setup>
import { useCounter } from '@/composables/useCounter'

const a = useCounter(0)
const b = useCounter(100)   // completely separate from a
</script>
```

This is the main benefit over **mixins** (Vue 2's reuse tool): you can see exactly where each value comes from, and there are no name collisions.

Composables can call other composables:

```js
// useUserSearch.js
import { ref, watch } from 'vue'
import { useDebounce } from './useDebounce'

export function useUserSearch() {
  const query = ref('')
  const debouncedQuery = useDebounce(query, 300)
  const results = ref([])

  watch(debouncedQuery, async (q) => {
    if (!q) return
    const res = await fetch(`https://jsonplaceholder.typicode.com/users?q=${q}`)
    results.value = await res.json()
  })

  return { query, results }
}
```

If you want state **shared** between components, declare it **outside** the function:

```js
// useSharedCount.js
import { ref } from 'vue'

const count = ref(0)   // created once, shared by everyone

export function useSharedCount() {
  const increment = () => count.value++
  return { count, increment }
}
```

For larger shared state, a Pinia store (Lesson 11) is a better fit.

---

## 6.4 Naming and organization

Conventions the Vue community follows:

| Convention | Example |
|---|---|
| Name starts with `use` | `useCounter`, `useFetch`, `useMouse` |
| One composable per file | `useCounter.js` |
| Folder for composables | `src/composables/` |
| Return an object of refs | `return { count, increment }` |
| Accept refs or plain values as arguments | `toValue(arg)` helper |

Recommended structure:

```
src/
├── components/
├── composables/
│   ├── useCounter.js
│   ├── useFetch.js
│   └── useLocalStorage.js
└── App.vue
```

Guidelines for good composables:

- **Call them synchronously** at the top level of `<script setup>` (not inside `if` blocks or after an `await`), because lifecycle hooks need to attach to the current component.
- **Clean up** side effects (event listeners, timers) in `onUnmounted`.
- **Return refs**, not raw values, so destructuring keeps reactivity.
- **Keep each one focused** on a single job.

Accepting both refs and plain values makes composables flexible. Use `toValue` (Vue 3.3+):

```js
import { toValue } from 'vue'

export function useTitle(title) {
  watchEffect(() => {
    document.title = toValue(title)   // works with ref, getter, or string
  })
}
```

The library [VueUse](https://vueuse.org) offers hundreds of well-tested composables. Check it before writing your own.

---

[Previous](./[5]-Events-And-Slots.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[7]-Lifecycle-Hooks.md)