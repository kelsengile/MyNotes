[Previous](./[10]-Vue-Router.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[12]-Data-Fetching.md)

*Building Applications*

# Lesson 11 - State Management With Pinia

## 11.1 Why shared state is needed

**State** is the data your app remembers. Sometimes many unrelated components need the same data, for example the logged-in user, a shopping cart, or a theme.

Without a shared store, you must pass props down through many levels and emit events back up:

```
App
├── Header          (needs cart count)
├── ProductList
│   └── ProductCard (needs "add to cart")
└── CartPage        (needs full cart)
```

This gets messy quickly. A **store** is a central place that any component can read from and write to directly.

**Pinia** is the official state management library for Vue 3. It is simple, works well with TypeScript, and has good developer-tools support.

Install it (or select it in `create-vue`):

```bash
npm install pinia
```

Register it in `main.js`:

```js
import { createApp } from 'vue'
import { createPinia } from 'pinia'
import App from './App.vue'

createApp(App).use(createPinia()).mount('#app')
```

Use a store for state that is truly **shared**. Data used by only one component should stay local in that component.

---

## 11.2 Defining stores

A store is defined with `defineStore`, which takes a unique id and a setup function (or options object). Create `src/stores/counter.js`:

```js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCounterStore = defineStore('counter', () => {
  // state → ref()
  const count = ref(0)

  // getters → computed()
  const double = computed(() => count.value * 2)

  // actions → functions
  function increment() {
    count.value++
  }

  return { count, double, increment }
})
```

This is the **setup store** syntax: it looks exactly like a composable. Pinia also supports an **options store**:

```js
export const useCounterStore = defineStore('counter', {
  state: () => ({ count: 0 }),
  getters: {
    double: (state) => state.count * 2
  },
  actions: {
    increment() {
      this.count++
    }
  }
})
```

Using the store in a component:

```vue
<script setup>
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()
</script>

<template>
  <p>{{ counter.count }} (double: {{ counter.double }})</p>
  <button @click="counter.increment()">+1</button>
</template>
```

Every component calling `useCounterStore()` gets the **same** instance, so they all stay in sync.

**Destructuring warning:** extracting state directly loses reactivity. Use `storeToRefs` for state and getters:

```js
import { storeToRefs } from 'pinia'

const counter = useCounterStore()
const { count, double } = storeToRefs(counter)   // reactive refs
const { increment } = counter                     // actions can be destructured normally
```

---

## 11.3 Actions and getters

**Getters** are computed values based on state. They are cached and recalculated only when their inputs change.

**Actions** are functions that modify state. Unlike mutations in older libraries, they can be asynchronous and can be called from anywhere.

Here is a realistic shopping cart store:

```js
// src/stores/cart.js
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

export const useCartStore = defineStore('cart', () => {
  const items = ref([])

  const totalItems = computed(() =>
    items.value.reduce((sum, item) => sum + item.quantity, 0)
  )

  const totalPrice = computed(() =>
    items.value.reduce((sum, item) => sum + item.price * item.quantity, 0)
  )

  function addItem(product) {
    const existing = items.value.find(i => i.id === product.id)
    if (existing) {
      existing.quantity++
    } else {
      items.value.push({ ...product, quantity: 1 })
    }
  }

  function removeItem(id) {
    items.value = items.value.filter(i => i.id !== id)
  }

  function clear() {
    items.value = []
  }

  return { items, totalItems, totalPrice, addItem, removeItem, clear }
})
```

Actions can be asynchronous:

```js
export const useUserStore = defineStore('user', () => {
  const user = ref(null)
  const loading = ref(false)
  const error = ref(null)

  async function fetchUser(id) {
    loading.value = true
    error.value = null
    try {
      const res = await fetch(`https://jsonplaceholder.typicode.com/users/${id}`)
      if (!res.ok) throw new Error('Request failed')
      user.value = await res.json()
    } catch (e) {
      error.value = e.message
    } finally {
      loading.value = false
    }
  }

  return { user, loading, error, fetchUser }
})
```

A store can use another store inside an action or getter by calling its `use...Store()` function.

---

## 11.4 Persisting state

Pinia state lives in memory, so it resets when the page reloads. To keep it, save it to `localStorage`.

**Manual approach** using a watcher inside the store:

```js
import { ref, watch } from 'vue'
import { defineStore } from 'pinia'

export const useThemeStore = defineStore('theme', () => {
  const theme = ref(localStorage.getItem('theme') || 'light')

  watch(theme, (value) => {
    localStorage.setItem('theme', value)
  })

  function toggle() {
    theme.value = theme.value === 'light' ? 'dark' : 'light'
  }

  return { theme, toggle }
})
```

**Plugin approach** using `pinia-plugin-persistedstate`:

```bash
npm install pinia-plugin-persistedstate
```

```js
// main.js
import { createPinia } from 'pinia'
import piniaPluginPersistedstate from 'pinia-plugin-persistedstate'

const pinia = createPinia()
pinia.use(piniaPluginPersistedstate)
```

Then enable it for a store:

```js
export const useCartStore = defineStore('cart', () => {
  const items = ref([])
  return { items }
}, {
  persist: true
})
```

Notes:

- `localStorage` stores **strings only**, so objects must be converted with `JSON.stringify` and `JSON.parse` (the plugin does this for you).
- Never store passwords or other sensitive secrets in `localStorage`.
- Check the plugin's documentation for options such as choosing which fields to persist.

---

[Previous](./[10]-Vue-Router.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[12]-Data-Fetching.md)