[Previous](./[3]-Reactivity-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[5]-Events-And-Slots.md)

*Components*

# Lesson 4 - Components And Props

## 4.1 Defining and registering components

A **component** is a reusable piece of UI with its own template, logic, and styles. You build an app by combining many small components.

Each component lives in its own `.vue` file. Here is `src/components/UserCard.vue`:

```vue
<script setup>
const name = 'Ada Lovelace'
</script>

<template>
  <div class="card">
    <h3>{{ name }}</h3>
  </div>
</template>

<style scoped>
.card {
  border: 1px solid #ccc;
  padding: 1rem;
  border-radius: 8px;
}
</style>
```

To use it, **import** it in the parent. With `<script setup>`, imported components are automatically available in the template:

```vue
<script setup>
import UserCard from './components/UserCard.vue'
</script>

<template>
  <UserCard />
  <UserCard />
</template>
```

Each `<UserCard />` creates its own independent instance.

Naming conventions:

- File names use **PascalCase**: `UserCard.vue`.
- Multi-word names avoid clashing with built-in HTML elements (`Card.vue` is risky, `UserCard.vue` is safe).
- A component with no children can be self-closing: `<UserCard />`.

---

## 4.2 Passing props

**Props** are custom attributes that let a parent pass data down to a child, which makes components reusable. In the child, declare props with `defineProps`:

```vue
<!-- UserCard.vue -->
<script setup>
const props = defineProps({
  name: String,
  age: Number
})
</script>

<template>
  <div class="card">
    <h3>{{ name }}</h3>
    <p>Age: {{ age }}</p>
  </div>
</template>
```

In the parent, pass the values:

```vue
<script setup>
import UserCard from './components/UserCard.vue'
</script>

<template>
  <!-- static string value -->
  <UserCard name="Ada" :age="36" />

  <!-- Use v-bind (:) for anything that isn't a plain string -->
  <UserCard name="Grace" :age="45" />
</template>
```

Without the `:`, the value is passed as a **string**. `age="36"` gives the string `"36"`, but `:age="36"` gives the number `36`.

Props can be bound to variables and used with `v-for`:

```vue
<script setup>
import { ref } from 'vue'
import UserCard from './components/UserCard.vue'

const users = ref([
  { id: 1, name: 'Ada', age: 36 },
  { id: 2, name: 'Grace', age: 45 }
])
</script>

<template>
  <UserCard v-for="user in users" :key="user.id" :name="user.name" :age="user.age" />
</template>
```

In JavaScript, prop names written in camelCase (`userName`) are passed in templates as kebab-case (`user-name`) or camelCase. Both work.

---

## 4.3 Prop validation

You can describe what each prop should look like. Vue warns you in the browser console during development if a value does not match:

```vue
<script setup>
defineProps({
  // basic type
  title: String,

  // required
  id: {
    type: Number,
    required: true
  },

  // default value
  size: {
    type: String,
    default: 'medium'
  },

  // several allowed types
  value: [String, Number],

  // objects and arrays need a factory function for defaults
  tags: {
    type: Array,
    default: () => []
  },

  // custom validator
  status: {
    type: String,
    validator: (value) => ['active', 'inactive', 'pending'].includes(value)
  }
})
</script>
```

Supported types: `String`, `Number`, `Boolean`, `Array`, `Object`, `Function`, `Date`, `Symbol`.

A `Boolean` prop has a handy shorthand: writing it with no value means `true`:

```vue
<MyButton disabled />   <!-- same as :disabled="true" -->
```

---

## 4.4 One-way data flow

Props follow a **one-way data flow**: data goes **down** from parent to child. A child must never change a prop directly:

```vue
<script setup>
const props = defineProps({ count: Number })

props.count++   // ❌ Wrong. Vue warns that props are readonly.
</script>
```

If the child needs a changeable version, there are two correct patterns.

**Pattern 1: use the prop as the starting value for local data:**

```js
import { ref } from 'vue'

const props = defineProps({ initialCount: Number })
const count = ref(props.initialCount)   // local copy
```

**Pattern 2: derive a new value with `computed`:**

```js
import { computed } from 'vue'

const props = defineProps({ name: String })
const shoutedName = computed(() => props.name.toUpperCase())
```

If the child needs to ask the parent to change data, it sends an **event** upward. This is covered in the next lesson.

```
Parent ──(props)──►  Child
Parent ◄─(events)──  Child
```

This pattern keeps data changes predictable. You can always trace where state is modified.

---

[Previous](./[3]-Reactivity-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[5]-Events-And-Slots.md)