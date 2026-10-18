[Previous](./[7]-Lifecycle-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[9]-Transitions-And-Directives.md)

*Components*

# Lesson 8 - Forms And User Input

## 8.1 v-model on inputs

`v-model` creates **two-way binding** between a form element and a variable. Typing updates the variable, and changing the variable updates the input.

```vue
<script setup>
import { ref } from 'vue'

const text = ref('')
const message = ref('')
const agreed = ref(false)
const colors = ref([])
const picked = ref('')
const country = ref('')
</script>

<template>
  <!-- text input -->
  <input v-model="text" placeholder="Type here" />
  <p>You typed: {{ text }}</p>

  <!-- textarea -->
  <textarea v-model="message"></textarea>

  <!-- single checkbox → boolean -->
  <label><input type="checkbox" v-model="agreed" /> I agree</label>

  <!-- multiple checkboxes → array -->
  <label><input type="checkbox" value="red" v-model="colors" /> Red</label>
  <label><input type="checkbox" value="blue" v-model="colors" /> Blue</label>
  <p>Selected: {{ colors }}</p>

  <!-- radio buttons -->
  <label><input type="radio" value="small" v-model="picked" /> Small</label>
  <label><input type="radio" value="large" v-model="picked" /> Large</label>

  <!-- select -->
  <select v-model="country">
    <option disabled value="">Choose one</option>
    <option value="ph">Philippines</option>
    <option value="us">United States</option>
  </select>
</template>
```

`v-model` is shorthand for binding the `value` and listening for the `input` event (the exact property and event depend on the element type).

For a full form, store values in a single `reactive` object:

```js
import { reactive } from 'vue'

const form = reactive({ name: '', email: '', age: null })
```

```vue
<input v-model="form.name" />
<input v-model="form.email" type="email" />
```

---

## 8.2 Modifiers like .lazy and .trim

Modifiers adjust how `v-model` behaves. Add them after `v-model` with a dot:

| Modifier | Effect |
|---|---|
| `.lazy` | Syncs on `change` (when focus leaves) instead of every keystroke |
| `.number` | Converts the input to a number automatically |
| `.trim` | Removes whitespace from the start and end |

```vue
<script setup>
import { ref } from 'vue'

const name = ref('')
const age = ref(0)
const bio = ref('')
</script>

<template>
  <input v-model.trim="name" />
  <input v-model.number="age" type="number" />
  <textarea v-model.lazy="bio"></textarea>
</template>
```

Modifiers can be combined: `v-model.lazy.trim="name"`.

Handling form submission uses the `.prevent` event modifier so the page does not reload:

```vue
<script setup>
import { reactive } from 'vue'

const form = reactive({ name: '', email: '' })

function submit() {
  console.log('Submitted:', { ...form })
}
</script>

<template>
  <form @submit.prevent="submit">
    <input v-model.trim="form.name" placeholder="Name" />
    <input v-model.trim="form.email" type="email" placeholder="Email" />
    <button type="submit">Send</button>
  </form>
</template>
```

---

## 8.3 Validation approaches

**Validation** checks that user input is acceptable before it is used. Here is a simple hand-written approach using `computed`:

```vue
<script setup>
import { reactive, computed, ref } from 'vue'

const form = reactive({ email: '', password: '' })
const submitted = ref(false)

const errors = computed(() => {
  const result = {}
  if (!form.email.includes('@')) {
    result.email = 'Enter a valid email address'
  }
  if (form.password.length < 8) {
    result.password = 'Password must be at least 8 characters'
  }
  return result
})

const isValid = computed(() => Object.keys(errors.value).length === 0)

function submit() {
  submitted.value = true
  if (!isValid.value) return
  alert('Form is valid!')
}
</script>

<template>
  <form @submit.prevent="submit" novalidate>
    <div>
      <input v-model.trim="form.email" type="email" placeholder="Email" />
      <small v-if="submitted && errors.email" style="color: red">{{ errors.email }}</small>
    </div>

    <div>
      <input v-model="form.password" type="password" placeholder="Password" />
      <small v-if="submitted && errors.password" style="color: red">{{ errors.password }}</small>
    </div>

    <button type="submit">Register</button>
  </form>
</template>
```

Good validation practices:

- **Validate on the client for convenience**, and **always validate again on the server** for security.
- Show errors **after** the user has interacted (on blur or submit), not while they are still typing their first character.
- Give clear, specific messages.
- Use the built-in HTML attributes (`required`, `minlength`, `type="email"`) as a first layer.

---

## 8.4 Form libraries

For larger forms, writing validation by hand becomes repetitive. Libraries help:

| Library | Description |
|---|---|
| **VeeValidate** | Popular, composable validation with Vue components or the Composition API |
| **FormKit** | Complete form framework with built-in inputs, validation, and generation from schemas |
| **Vuelidate** | Lightweight validation rules for models |
| **Zod / Yup** | Schema libraries (not Vue-specific) that define validation rules; they can be plugged into VeeValidate |

Example with VeeValidate and Zod (install with `npm install vee-validate @vee-validate/zod zod`):

```vue
<script setup>
import { useForm } from 'vee-validate'
import { toTypedSchema } from '@vee-validate/zod'
import { z } from 'zod'

const schema = toTypedSchema(
  z.object({
    email: z.string().email('Invalid email'),
    password: z.string().min(8, 'At least 8 characters')
  })
)

const { defineField, errors, handleSubmit } = useForm({ validationSchema: schema })

const [email] = defineField('email')
const [password] = defineField('password')

const onSubmit = handleSubmit((values) => {
  console.log('Valid data:', values)
})
</script>

<template>
  <form @submit="onSubmit">
    <input v-model="email" placeholder="Email" />
    <span>{{ errors.email }}</span>

    <input v-model="password" type="password" placeholder="Password" />
    <span>{{ errors.password }}</span>

    <button>Submit</button>
  </form>
</template>
```

Library APIs change between versions, so check each library's official documentation for the current syntax. Choose a library when your forms have many fields, complex rules, or dynamic sections. For a login or a small contact form, hand-written validation is perfectly fine.

---

[Previous](./[7]-Lifecycle-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Vue.js.md) | [Next](./[9]-Transitions-And-Directives.md)