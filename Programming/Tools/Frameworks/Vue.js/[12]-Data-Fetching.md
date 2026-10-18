[Previous](./[11]-State-Management-With-Pinia.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[13]-Styling-Vue-Components.md)

*Building Applications*

# Lesson 12 - Data Fetching

## 12.1 Fetching with fetch and Axios

Most apps load data from an **API** (a server that returns data, usually as JSON). There are two common tools.

**The built-in `fetch`:**

```vue
<script setup>
import { ref, onMounted } from 'vue'

const posts = ref([])

onMounted(async () => {
  const response = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5')
  posts.value = await response.json()
})
</script>

<template>
  <ul>
    <li v-for="post in posts" :key="post.id">{{ post.title }}</li>
  </ul>
</template>
```

Important: `fetch` does **not** throw an error for HTTP failures like 404 or 500. You must check `response.ok` yourself.

**Axios** is a popular library with extra conveniences:

```bash
npm install axios
```

```js
import axios from 'axios'

const { data } = await axios.get('https://jsonplaceholder.typicode.com/posts', {
  params: { _limit: 5 }
})
console.log(data)
```

| Feature | `fetch` | Axios |
|---|---|---|
| Installation | Built into browsers | `npm install axios` |
| JSON parsing | Manual (`response.json()`) | Automatic (`response.data`) |
| HTTP errors | Must check `response.ok` | Throws automatically |
| Interceptors | No | Yes |

Sending data with a POST request:

```js
await fetch('https://jsonplaceholder.typicode.com/posts', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ title: 'Hello', body: 'World', userId: 1 })
})
```

---

## 12.2 Loading and error states

Network requests take time and can fail. A good UI handles three situations: **loading**, **error**, and **success**.

```vue
<script setup>
import { ref, onMounted } from 'vue'

const posts = ref([])
const loading = ref(true)
const error = ref(null)

onMounted(async () => {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5')
    if (!response.ok) {
      throw new Error(`Server responded with ${response.status}`)
    }
    posts.value = await response.json()
  } catch (e) {
    error.value = e.message
  } finally {
    loading.value = false   // runs whether it succeeded or failed
  }
})
</script>

<template>
  <p v-if="loading">Loading...</p>
  <p v-else-if="error" style="color: red">Something went wrong: {{ error }}</p>
  <p v-else-if="posts.length === 0">No posts found.</p>
  <ul v-else>
    <li v-for="post in posts" :key="post.id">{{ post.title }}</li>
  </ul>
</template>
```

The order of `v-if` / `v-else-if` / `v-else` matters: check loading first, then error, then empty, then the data.

Tips:

- Always reset `error` to `null` before starting a new request.
- Show a way to **retry** after an error.
- Disable submit buttons while a request is running to prevent duplicates.

---

## 12.3 Composable-based fetching

Loading, error, and data logic repeats in every component, so extract it into a composable (see Lesson 6). Create `src/composables/useFetch.js`:

```js
import { ref, watchEffect, toValue } from 'vue'

export function useFetch(url) {
  const data = ref(null)
  const error = ref(null)
  const loading = ref(false)

  async function load() {
    loading.value = true
    data.value = null
    error.value = null

    try {
      const response = await fetch(toValue(url))
      if (!response.ok) throw new Error(`HTTP ${response.status}`)
      data.value = await response.json()
    } catch (e) {
      error.value = e.message
    } finally {
      loading.value = false
    }
  }

  // re-fetches automatically when `url` is a ref/getter that changes
  watchEffect(load)

  return { data, error, loading, refetch: load }
}
```

Using it:

```vue
<script setup>
import { ref, computed } from 'vue'
import { useFetch } from '@/composables/useFetch'

const id = ref(1)
const url = computed(() => `https://jsonplaceholder.typicode.com/users/${id.value}`)

const { data: user, error, loading } = useFetch(url)
</script>

<template>
  <button @click="id++">Next user</button>
  <p v-if="loading">Loading...</p>
  <p v-else-if="error">Error: {{ error }}</p>
  <div v-else-if="user">
    <h2>{{ user.name }}</h2>
    <p>{{ user.email }}</p>
  </div>
</template>
```

Changing `id` changes `url`, which makes the composable fetch again automatically.

A production-ready version should also cancel outdated requests with `AbortController`, so a slow old response cannot overwrite a newer one (see the watcher cleanup example in Lesson 7).

---

## 12.4 Caching with Vue Query

Hand-written fetching does not cache results, refetch stale data, retry failures, or share requests between components. **TanStack Query for Vue** (Vue Query) handles all of this.

```bash
npm install @tanstack/vue-query
```

Register it in `main.js`:

```js
import { createApp } from 'vue'
import { VueQueryPlugin } from '@tanstack/vue-query'
import App from './App.vue'

createApp(App).use(VueQueryPlugin).mount('#app')
```

Fetching data with `useQuery`:

```vue
<script setup>
import { useQuery } from '@tanstack/vue-query'

async function fetchPosts() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts?_limit=5')
  if (!res.ok) throw new Error('Failed to load posts')
  return res.json()
}

const { data, isPending, isError, error, refetch } = useQuery({
  queryKey: ['posts'],     // unique cache key
  queryFn: fetchPosts
})
</script>

<template>
  <p v-if="isPending">Loading...</p>
  <p v-else-if="isError">Error: {{ error.message }}</p>
  <ul v-else>
    <li v-for="post in data" :key="post.id">{{ post.title }}</li>
  </ul>
  <button @click="refetch()">Refresh</button>
</template>
```

What you get automatically:

- **Caching:** two components using the same `queryKey` share one request.
- **Background refetching** when data is stale or when the window regains focus.
- **Retries** on failure.
- **Mutations** (`useMutation`) for POST, PUT, and DELETE, with cache invalidation after they succeed.

Use Vue Query when your app depends heavily on server data. For one or two simple requests, plain `fetch` in a composable is enough.

---

[Previous](./[11]-State-Management-With-Pinia.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[13]-Styling-Vue-Components.md)