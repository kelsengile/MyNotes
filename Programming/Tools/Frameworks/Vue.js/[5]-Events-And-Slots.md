[Previous](./[4]-Components-And-Props.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[6]-Composition-API-And-Composables.md)

*Components*

# Lesson 5 - Events And Slots

## 5.1 Emitting events

While props send data **down**, **events** send messages **up** from a child to its parent. The child declares events with `defineEmits` and fires them with `emit`:

```vue
<!-- AddButton.vue -->
<script setup>
const emit = defineEmits(['add'])

function handleClick() {
  emit('add', 5)   // event name, then any payload
}
</script>

<template>
  <button @click="handleClick">Add 5</button>
</template>
```

The parent listens with `v-on` (`@`):

```vue
<!-- Parent.vue -->
<script setup>
import { ref } from 'vue'
import AddButton from './AddButton.vue'

const total = ref(0)

function onAdd(amount) {
  total.value += amount
}
</script>

<template>
  <p>Total: {{ total }}</p>
  <AddButton @add="onAdd" />
</template>
```

You can also emit directly in the template: `@click="$emit('add', 5)"`.

Event names written in camelCase in `emit('itemSaved')` are listened to as `@item-saved` (kebab-case) in templates.

You can validate event payloads using the object syntax:

```js
const emit = defineEmits({
  add: (amount) => typeof amount === 'number'   // return false to warn
})
```

---

## 5.2 v-model on components

`v-model` creates **two-way binding**. On a component it is shorthand for a prop plus an update event. In Vue 3.4 and later, the `defineModel()` macro makes this simple:

```vue
<!-- CustomInput.vue -->
<script setup>
const model = defineModel()
</script>

<template>
  <input v-model="model" />
</template>
```

```vue
<!-- Parent.vue -->
<script setup>
import { ref } from 'vue'
import CustomInput from './CustomInput.vue'

const name = ref('Ada')
</script>

<template>
  <CustomInput v-model="name" />
  <p>Hello, {{ name }}</p>
</template>
```

Typing in the input updates `name` in the parent, and changing `name` in the parent updates the input.

For multiple bindings, give each a name:

```vue
<!-- UserForm.vue -->
<script setup>
const firstName = defineModel('firstName')
const lastName = defineModel('lastName')
</script>

<template>
  <input v-model="firstName" />
  <input v-model="lastName" />
</template>
```

```vue
<UserForm v-model:first-name="first" v-model:last-name="last" />
```

Under the hood, `defineModel` is equivalent to declaring a `modelValue` prop and an `update:modelValue` event.

---

## 5.3 Slots and scoped slots

Props pass data. **Slots** let a parent pass **template content** into a child, which is useful for flexible layouts.

**Basic slot:**

```vue
<!-- BaseCard.vue -->
<template>
  <div class="card">
    <slot>Default content if nothing is provided</slot>
  </div>
</template>
```

```vue
<BaseCard>
  <h3>Hello</h3>
  <p>This content replaces the slot.</p>
</BaseCard>
```

**Named slots** let you place content in different locations. Use `v-slot:name` or the shorthand `#name`:

```vue
<!-- BaseLayout.vue -->
<template>
  <header><slot name="header" /></header>
  <main><slot /></main>
  <footer><slot name="footer" /></footer>
</template>
```

```vue
<BaseLayout>
  <template #header>
    <h1>Page title</h1>
  </template>

  <p>Main content goes in the default slot.</p>

  <template #footer>
    <small>© 2026</small>
  </template>
</BaseLayout>
```

**Scoped slots** let the child pass data **back to** the slot content:

```vue
<!-- ItemList.vue -->
<script setup>
defineProps({ items: Array })
</script>

<template>
  <ul>
    <li v-for="item in items" :key="item.id">
      <slot :item="item" :index="item.id">{{ item.name }}</slot>
    </li>
  </ul>
</template>
```

```vue
<ItemList :items="fruits">
  <template #default="{ item }">
    <strong>{{ item.name }}</strong>
  </template>
</ItemList>
```

The child controls the data, and the parent decides how to display it.

---

## 5.4 Provide and inject

Passing props through many levels of components is called **prop drilling**. `provide` and `inject` let an ancestor share data with any descendant, however deep:

```vue
<!-- Ancestor.vue -->
<script setup>
import { ref, provide } from 'vue'

const theme = ref('dark')

provide('theme', theme)   // key, value
</script>
```

```vue
<!-- DeepChild.vue (any level below) -->
<script setup>
import { inject } from 'vue'

const theme = inject('theme', 'light')   // key, default value
</script>

<template>
  <p>Current theme: {{ theme }}</p>
</template>
```

```
Ancestor ── provide('theme') ─┐
   └─ Child                   │
       └─ GrandChild          │
           └─ DeepChild ◄─────┘ inject('theme')
```

Tips:

- Provide **refs** to keep the injected value reactive.
- To avoid unexpected changes, provide a function that updates the value instead of letting descendants change it freely:

```js
const theme = ref('dark')
function toggleTheme() {
  theme.value = theme.value === 'dark' ? 'light' : 'dark'
}
provide('theme', { theme, toggleTheme })
```

- Use provide and inject sparingly. For app-wide shared state, a store like Pinia (Lesson 11) is usually clearer.

---

[Previous](./[4]-Components-And-Props.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[6]-Composition-API-And-Composables.md)