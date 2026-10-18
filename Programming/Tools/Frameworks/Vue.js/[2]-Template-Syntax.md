[Previous](./[1]-What-Is-Vue.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[3]-Reactivity-Fundamentals.md)

*Basics*

# Lesson 2 - Template Syntax

## 2.1 Interpolation

**Interpolation** displays data inside the template using double curly braces (also called "mustache" syntax):

```vue
<script setup>
const name = 'Ada'
const price = 20
</script>

<template>
  <p>Hello, {{ name }}!</p>
  <p>Total: {{ price * 2 }}</p>
  <p>{{ name.toUpperCase() }}</p>
  <p>{{ price > 10 ? 'Expensive' : 'Cheap' }}</p>
</template>
```

Inside `{{ }}` you can write any **single JavaScript expression**: math, method calls, ternaries. You cannot write statements such as `if` or `let`.

By default, interpolation treats content as plain text. To render real HTML, use the `v-html` directive:

```vue
<script setup>
const rawHtml = '<strong>Bold text</strong>'
</script>

<template>
  <p v-html="rawHtml"></p>
</template>
```

> **Warning:** Never use `v-html` with content from users. It can lead to cross-site scripting (XSS) attacks.

---

## 2.2 Directives like v-bind and v-on

**Directives** are special attributes that start with `v-`. They tell Vue to do something with the element.

**`v-bind`** sets an HTML attribute from data. The shorthand is `:`

```vue
<script setup>
import { ref } from 'vue'

const imageUrl = ref('https://vuejs.org/images/logo.png')
const isDisabled = ref(true)
const isActive = ref(true)
</script>

<template>
  <img v-bind:src="imageUrl" alt="Logo" width="50" />
  <img :src="imageUrl" alt="Logo" width="50" /> <!-- shorthand -->
  <button :disabled="isDisabled">Can't click</button>
  <div :class="{ active: isActive }">Conditional class</div>
  <div :style="{ color: 'red', fontSize: '20px' }">Styled</div>
</template>
```

**`v-on`** listens for events. The shorthand is `@`

```vue
<script setup>
import { ref } from 'vue'

const count = ref(0)

function greet(event) {
  alert('Hello! Tag: ' + event.target.tagName)
}
</script>

<template>
  <button v-on:click="count++">Clicked {{ count }} times</button>
  <button @click="greet">Greet</button> <!-- shorthand with a method -->
  <form @submit.prevent="greet">      <!-- .prevent calls preventDefault() -->
    <button>Submit</button>
  </form>
</template>
```

Common event modifiers: `.prevent`, `.stop`, `.once`. Key modifiers include `@keyup.enter`.

---

## 2.3 Conditional rendering with v-if

`v-if` adds or removes an element from the page depending on a condition:

```vue
<script setup>
import { ref } from 'vue'

const score = ref(75)
const visible = ref(true)
</script>

<template>
  <p v-if="score >= 90">Grade A</p>
  <p v-else-if="score >= 70">Grade B</p>
  <p v-else>Keep practicing</p>

  <p v-show="visible">Toggled with CSS display</p>
</template>
```

| Directive | What it does | Best for |
|---|---|---|
| `v-if` | Creates or destroys the element | Conditions that rarely change |
| `v-show` | Keeps the element, toggles `display: none` | Things toggled often |

`v-else-if` and `v-else` must come directly after a `v-if` or `v-else-if` element.

To conditionally render several elements without an extra wrapper, put `v-if` on a `<template>` tag:

```vue
<template v-if="loggedIn">
  <h2>Welcome back</h2>
  <p>Your dashboard is ready.</p>
</template>
```

---

## 2.4 List rendering with v-for

`v-for` repeats an element for each item in an array:

```vue
<script setup>
import { ref } from 'vue'

const fruits = ref([
  { id: 1, name: 'Apple' },
  { id: 2, name: 'Banana' },
  { id: 3, name: 'Cherry' }
])
</script>

<template>
  <ul>
    <li v-for="(fruit, index) in fruits" :key="fruit.id">
      {{ index + 1 }}. {{ fruit.name }}
    </li>
  </ul>
</template>
```

Always provide a unique **`:key`**. It lets Vue track each item when the list changes, so it can reorder and update elements correctly. Use a stable id, not the array index, when items can be added, removed, or reordered.

`v-for` also works with objects and numbers:

```vue
<script setup>
const user = { name: 'Ada', role: 'Engineer' }
</script>

<template>
  <p v-for="(value, key) in user" :key="key">{{ key }}: {{ value }}</p>
  <span v-for="n in 5" :key="n">{{ n }} </span>
</template>
```

Avoid using `v-if` and `v-for` on the same element. Filter the list first with a computed property (see Lesson 3) or wrap the item in a `<template v-for>`.

---

[Previous](./[1]-What-Is-Vue.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[3]-Reactivity-Fundamentals.md)