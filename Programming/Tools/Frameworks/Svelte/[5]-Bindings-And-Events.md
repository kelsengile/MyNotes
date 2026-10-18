[Previous](./[4]-Props-And-Component-Communication.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[6]-Shared-State-And-Stores.md)

*Components In Depth*

# Lesson 5 - Bindings And Events

## 5.1 bind:value and friends

A **binding** keeps a variable and a DOM property in sync in both directions. When the user types, the variable updates, and when the variable changes, the input updates.

```svelte
<script>
  let name = $state('');
  let agreed = $state(false);
  let flavor = $state('vanilla');
  let size = $state(1);
</script>

<input bind:value={name} placeholder="Your name" />
<p>Hello {name || 'stranger'}</p>

<label><input type="checkbox" bind:checked={agreed} /> I agree</label>

<select bind:value={flavor}>
  <option value="vanilla">Vanilla</option>
  <option value="chocolate">Chocolate</option>
</select>

<!-- Number and range inputs give you a real number, not a string -->
<input type="range" min="1" max="10" bind:value={size} />
```

Common bindings:

| Binding | Works on | Gives you |
|---|---|---|
| `bind:value` | input, textarea, select | The current value |
| `bind:checked` | checkbox | `true` or `false` |
| `bind:group` | radio and checkbox groups | The selected value (or an array of values) |
| `bind:this` | any element or component | A reference to the DOM element |
| `bind:clientWidth` / `bind:clientHeight` | any element | Read-only size |
| `bind:files` | file input | A `FileList` |

**Groups:**

```svelte
<script>
  let color = $state('red');
  let toppings = $state([]);
</script>

<label><input type="radio" bind:group={color} value="red" /> Red</label>
<label><input type="radio" bind:group={color} value="blue" /> Blue</label>

<label><input type="checkbox" bind:group={toppings} value="cheese" /> Cheese</label>
<label><input type="checkbox" bind:group={toppings} value="olives" /> Olives</label>
```

**Function bindings** let you transform values as they move in and out. Pass a getter and a setter:

```svelte
<input bind:value={() => name, (v) => (name = v.trim())} />
```

Component bindings only work with props marked `$bindable` (see Lesson 4).

---

## 5.2 Event handling

Events are attributes named `on` followed by the event name: `onclick`, `oninput`, `onkeydown`, `onsubmit`, and so on.

```svelte
<script>
  let count = $state(0);

  function increment() {
    count += 1;
  }

  function handleKey(event) {
    if (event.key === 'Enter') count = 0;
  }
</script>

<!-- Pass a function reference -->
<button onclick={increment}>Add</button>

<!-- Or write an inline arrow function -->
<button onclick={() => (count -= 1)}>Subtract</button>

<input onkeydown={handleKey} placeholder="Press Enter to reset" />

<p>{count}</p>
```

Points to remember:

- The handler receives the standard browser **event object**.
- Svelte 5 has no `|preventDefault` style modifiers. Call the method yourself: `event.preventDefault()`.
- The shorthand works too: when a function is named `onclick`, you can write `<button {onclick}>`.
- To listen on the window or document, use special elements instead of manual listeners:

```svelte
<svelte:window onkeydown={handleKey} />
<svelte:document onvisibilitychange={() => console.log('tab changed')} />
```

Svelte removes these listeners automatically when the component is destroyed.

---

## 5.3 Forms

Combine bindings and events to build forms without extra libraries.

```svelte
<script>
  let email = $state('');
  let password = $state('');
  let role = $state('student');
  let accepted = $state(false);
  let message = $state('');

  let isValid = $derived(email.includes('@') && password.length >= 8 && accepted);

  function handleSubmit(event) {
    event.preventDefault(); // stop the browser from reloading the page
    message = `Welcome, ${email} (${role})`;
  }
</script>

<form onsubmit={handleSubmit}>
  <label>
    Email
    <input type="email" bind:value={email} required />
  </label>

  <label>
    Password (min 8 characters)
    <input type="password" bind:value={password} required minlength="8" />
  </label>

  <fieldset>
    <legend>Role</legend>
    <label><input type="radio" bind:group={role} value="student" /> Student</label>
    <label><input type="radio" bind:group={role} value="teacher" /> Teacher</label>
  </fieldset>

  <label>
    <input type="checkbox" bind:checked={accepted} />
    I accept the terms
  </label>

  <button type="submit" disabled={!isValid}>Sign up</button>
</form>

<p>{message}</p>
```

Tips:

- Use real `<form>` elements and `<button type="submit">`. They give you the Enter key and accessibility for free.
- Use `$derived` for validation results.
- Always connect labels to inputs, either by wrapping the input in the `<label>` or with matching `for` and `id` attributes.
- In SvelteKit, forms can also be handled on the server (see Lesson 11).

---

## 5.4 Actions with use:

An **action** is a function that runs when an element is mounted. It lets you reuse DOM logic, such as tooltips or "click outside" detection, across many elements. Attach one with `use:`.

```js
// clickOutside.js
export function clickOutside(node, callback) {
  function handleClick(event) {
    if (!node.contains(event.target)) callback();
  }

  document.addEventListener('click', handleClick, true);

  return {
    destroy() {
      document.removeEventListener('click', handleClick, true);
    }
  };
}
```

```svelte
<script>
  import { clickOutside } from './clickOutside.js';

  let open = $state(true);
</script>

{#if open}
  <div class="menu" use:clickOutside={() => (open = false)}>
    Click anywhere outside to close me.
  </div>
{/if}
```

How actions work:

- The first argument is the **DOM node**. The second is the optional value you pass after `=`.
- The function may return an object with `destroy()` for cleanup and, in older style, `update(newValue)` for when the parameter changes.
- Use an action when you need direct DOM access that bindings and events do not cover.
- Newer versions of Svelte also offer **attachments** (`{@attach ...}`) as a more flexible alternative to actions. Check the official docs for the version you use.

[Previous](./[4]-Props-And-Component-Communication.md) | [Table of Contents](./[0]-Introduction-to-Svelte.md) | [Next](./[6]-Shared-State-And-Stores.md)