[Previous](./[2]-Template-Syntax.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[4]-Components-And-Props.md)

*Basics*

# Lesson 3 - Reactivity Fundamentals

## 3.1 ref and reactive

**Reactivity** means that when your data changes, Vue automatically updates the page. You make data reactive with `ref()` or `reactive()`.

**`ref()`** works with any type of value. It wraps the value in an object with a `.value` property:

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++   // use .value in JavaScript
}
</script>

<template>
  <!-- no .value needed in the template -->
  <button @click="increment">Count: {{ count }}</button>
</template>
```

**`reactive()`** makes an object reactive, with no `.value` needed:

```js
import { reactive } from 'vue'

const user = reactive({ name: 'Ada', age: 30 })
user.age++          // triggers an update
```

| | `ref` | `reactive` |
|---|---|---|
| Works with | Any value (numbers, strings, objects) | Objects and arrays only |
| Access in JS | `.value` | Direct |
| Can replace whole value | Yes (`state.value = {...}`) | No (loses reactivity) |

Most developers prefer `ref()` for everything because it behaves consistently.

---

## 3.2 Computed properties

A **computed property** is a value derived from other reactive data. Vue caches it and only recalculates when its dependencies change:

```vue
<script setup>
import { ref, computed } from 'vue'

const firstName = ref('Ada')
const lastName = ref('Lovelace')

const fullName = computed(() => `${firstName.value} ${lastName.value}`)

const numbers = ref([1, 2, 3, 4, 5, 6])
const evens = computed(() => numbers.value.filter(n => n % 2 === 0))
</script>

<template>
  <p>{{ fullName }}</p>
  <p>{{ evens }}</p>
</template>
```

Computed vs a plain method:

- A **method** runs every time it is called.
- A **computed** runs only when its inputs change, and returns the cached result otherwise.

Computed properties should be **pure**: they should only calculate and return a value, with no side effects such as changing other data or calling an API.

A computed is read-only by default. A writable one takes `get` and `set`:

```js
const fullName = computed({
  get: () => `${firstName.value} ${lastName.value}`,
  set: (value) => {
    [firstName.value, lastName.value] = value.split(' ')
  }
})
```

---

## 3.3 Watchers

A **watcher** runs code (a side effect) when reactive data changes, for example saving to storage or calling an API.

**`watch()`** observes a specific source:

```vue
<script setup>
import { ref, watch } from 'vue'

const question = ref('')
const answer = ref('Ask a question')

watch(question, (newValue, oldValue) => {
  if (newValue.endsWith('?')) {
    answer.value = 'Thinking...'
  }
})
</script>

<template>
  <input v-model="question" placeholder="Type a question ending in ?" />
  <p>{{ answer }}</p>
</template>
```

To watch a property of a reactive object, use a getter function:

```js
watch(() => user.age, (newAge) => console.log('Age is now', newAge))
```

Useful options:

```js
watch(source, callback, {
  immediate: true,  // run once right away
  deep: true        // also detect nested changes
})
```

**`watchEffect()`** runs immediately and automatically tracks every reactive value used inside it:

```js
import { ref, watchEffect } from 'vue'

const id = ref(1)

watchEffect(() => {
  console.log('Current id:', id.value)  // re-runs whenever id changes
})
```

Use `computed` when you need a **value**. Use `watch` when you need a **side effect**.

---

## 3.4 How Vue tracks changes

Vue 3 uses JavaScript **Proxies**. When you create a `ref` or `reactive` object, Vue wraps it in a Proxy that intercepts:

- **get**: reading a property. Vue records which running effect (a template render, `computed`, or `watch`) is using it. This is called *tracking*.
- **set**: writing a property. Vue finds every effect that depends on it and re-runs them. This is called *triggering*.

```
Template render reads count.value  ──► Vue records: "this render depends on count"
count.value++                      ──► Vue triggers: "re-run that render"
```

Updates to the DOM are **batched**. If you change data several times in a row, Vue updates the page once. To run code after the DOM has updated, use `nextTick`:

```js
import { ref, nextTick } from 'vue'

const message = ref('Hello')

async function change() {
  message.value = 'Changed'
  await nextTick()
  console.log('DOM is updated now')
}
```

**Common pitfall: destructuring loses reactivity.**

```js
const state = reactive({ count: 0 })
let { count } = state   // plain number, no longer reactive!
```

Fix it with `toRefs`:

```js
import { reactive, toRefs } from 'vue'

const state = reactive({ count: 0 })
const { count } = toRefs(state)   // count is a ref linked to state.count
count.value++                     // state.count updates too
```

---

[Previous](./[2]-Template-Syntax.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[4]-Components-And-Props.md)