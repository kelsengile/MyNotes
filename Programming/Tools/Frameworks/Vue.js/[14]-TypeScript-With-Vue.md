[Previous](./[13]-Styling-Vue-Components.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[15]-Nuxt-And-Server-Side-Rendering.md)

*Building Applications*

# Lesson 14 - TypeScript With Vue

## 14.1 Typing props and emits

**TypeScript** adds types to JavaScript so many mistakes are caught while you write code instead of when the app runs. Vue 3 has first-class TypeScript support. Enable it by adding `lang="ts"` to the script block (or choosing TypeScript in `create-vue`):

```vue
<script setup lang="ts">
// TypeScript is now enabled
</script>
```

**Typing props**: pass a type to `defineProps`:

```vue
<script setup lang="ts">
interface User {
  id: number
  name: string
}

const props = defineProps<{
  title: string
  count?: number     // ? means optional
  user: User
}>()
</script>
```

Default values for optional props use `withDefaults`:

```ts
const props = withDefaults(defineProps<{
  title: string
  size?: 'small' | 'medium' | 'large'
}>(), {
  size: 'medium'
})
```

(In Vue 3.5+, you can also use reactive props destructure defaults: `const { size = 'medium' } = defineProps<...>()`.)

**Typing emits**: describe each event and its payload:

```vue
<script setup lang="ts">
const emit = defineEmits<{
  (e: 'add', amount: number): void
  (e: 'reset'): void
}>()

emit('add', 5)       // ✅
emit('add', 'five')  // ❌ TypeScript error
</script>
```

A shorter syntax is available in Vue 3.3+:

```ts
const emit = defineEmits<{
  add: [amount: number]
  reset: []
}>()
```

**Typing `defineModel`:**

```ts
const model = defineModel<string>()
```

---

## 14.2 Typing refs and stores

**Refs** infer their type from the initial value:

```ts
import { ref } from 'vue'

const count = ref(0)            // Ref<number>
count.value = 'hello'           // ❌ error: string is not assignable to number
```

When the initial value does not show the full type, add a generic:

```ts
import { ref } from 'vue'

interface User {
  id: number
  name: string
}

const user = ref<User | null>(null)
const ids = ref<number[]>([])

user.value = { id: 1, name: 'Ada' }
console.log(user.value?.name)   // optional chaining because it may be null
```

**Computed** values are inferred from the return value, or typed explicitly:

```ts
import { computed } from 'vue'

const double = computed<number>(() => count.value * 2)
```

**Template refs:**

```vue
<script setup lang="ts">
import { ref, onMounted } from 'vue'

const input = ref<HTMLInputElement | null>(null)

onMounted(() => input.value?.focus())
</script>

<template>
  <input ref="input" />
</template>
```

**Event handlers:**

```ts
function onInput(event: Event) {
  const target = event.target as HTMLInputElement
  console.log(target.value)
}
```

**Pinia stores** are typed automatically when you use the setup syntax:

```ts
import { ref, computed } from 'vue'
import { defineStore } from 'pinia'

interface Todo {
  id: number
  text: string
  done: boolean
}

export const useTodoStore = defineStore('todos', () => {
  const todos = ref<Todo[]>([])
  const remaining = computed(() => todos.value.filter(t => !t.done).length)

  function add(text: string) {
    todos.value.push({ id: Date.now(), text, done: false })
  }

  return { todos, remaining, add }
})
```

Components using `useTodoStore()` now get autocompletion and type checking for `todos`, `remaining`, and `add`.

---

## 14.3 Generic components

A **generic component** works with different data types while staying type-safe. Add the `generic` attribute to `<script setup>`:

```vue
<!-- TypedList.vue -->
<script setup lang="ts" generic="T extends { id: number }">
defineProps<{
  items: T[]
}>()

defineSlots<{
  default(props: { item: T }): any
}>()
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <slot :item="item" />
    </li>
  </ul>
</template>
```

Using it:

```vue
<script setup lang="ts">
import TypedList from './TypedList.vue'

const users = [
  { id: 1, name: 'Ada' },
  { id: 2, name: 'Grace' }
]
</script>

<template>
  <TypedList :items="users">
    <template #default="{ item }">
      {{ item.name }}   <!-- TypeScript knows item has name -->
    </template>
  </TypedList>
</template>
```

The type `T` is **inferred** from the array you pass, so `item` is correctly typed in the slot. `T extends { id: number }` is a **constraint**: any array passed must contain objects with an `id` number.

Other helpful typing tools:

| Tool | Purpose |
|---|---|
| `defineSlots<{...}>()` | Types slot props |
| `InjectionKey<T>` | Type-safe `provide` / `inject` |
| `PropType<T>` | Types props when using the runtime object syntax |

Example of a typed injection key:

```ts
import type { InjectionKey, Ref } from 'vue'

export const themeKey: InjectionKey<Ref<string>> = Symbol('theme')
// provide(themeKey, ref('dark'))
// const theme = inject(themeKey)   // typed as Ref<string> | undefined
```

---

## 14.4 Volar and tooling

TypeScript does not understand `.vue` files by itself. The tooling below bridges that gap.

**Volar** is the language tooling for Vue. In VS Code, it is provided by the **Vue - Official** extension. It gives you:

- Type checking and autocompletion inside `<template>` and `<script>`
- Props and event hints on components
- Go-to-definition between components

If you use Vue - Official, disable the old **Vetur** extension (it targets Vue 2).

**Type checking from the command line** with `vue-tsc`:

```bash
npm install -D vue-tsc typescript
npx vue-tsc --noEmit
```

Projects created with `create-vue` and TypeScript already include a script:

```json
{
  "scripts": {
    "type-check": "vue-tsc --build"
  }
}
```

Vite itself **does not** type check during `npm run dev` (it only strips types for speed). So run `vue-tsc` in your editor, in a build step, or in CI.

A tip on `tsconfig`: keep `"strict": true`. It enables the checks that make TypeScript worthwhile. If you are migrating an older project, enable strictness gradually.

Useful habits:

- Define shared types (like `User`) in a `src/types/` folder and import them with `import type`.
- Avoid `any`. If you must, prefer `unknown` and narrow it.
- Let TypeScript infer types where it can, and annotate where it cannot.

---

[Previous](./[13]-Styling-Vue-Components.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[15]-Nuxt-And-Server-Side-Rendering.md)