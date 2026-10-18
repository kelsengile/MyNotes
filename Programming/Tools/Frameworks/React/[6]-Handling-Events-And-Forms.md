[Previous](./[5]-Effects-And-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[7]-More-Built-In-Hooks.md)

*State And Hooks*

# Lesson 6 - Handling Events And Forms

## 6.1 Event handlers

You respond to user actions by passing a **function** to an event prop such as `onClick`, `onChange`, or `onSubmit`. Event props are camelCase.

```jsx
function SaveButton() {
  function handleClick() {
    alert("Saved!");
  }

  return <button onClick={handleClick}>Save</button>;
}
```

### Pass the function, don't call it

```jsx
<button onClick={handleClick}>OK</button>     // Right: passes the function
<button onClick={handleClick()}>Oops</button> // Wrong: calls it during render
```

### Passing arguments

Wrap the call in an arrow function:

```jsx
<button onClick={() => deleteItem(item.id)}>Delete</button>
```

### The event object

React gives your handler a **synthetic event** that works the same in all browsers:

```jsx
function handleChange(event) {
  console.log(event.target.value);  // the input's current text
  console.log(event.type);          // "change"
}
```

### Common events

| Event | Fires when |
|---|---|
| `onClick` | An element is clicked |
| `onChange` | An input's value changes (on every keystroke in React) |
| `onSubmit` | A form is submitted |
| `onKeyDown` / `onKeyUp` | A key is pressed / released |
| `onFocus` / `onBlur` | An element gains / loses focus |
| `onMouseEnter` / `onMouseLeave` | The pointer enters / leaves |

### preventDefault and stopPropagation

```jsx
function handleSubmit(event) {
  event.preventDefault();   // stop the browser's default action (page reload)
}

function handleInnerClick(event) {
  event.stopPropagation();  // stop the event bubbling up to parent handlers
}
```

### Event handlers can change state

Handlers are the most common place to call `setState`, as they are where user actions are turned into data changes:

```jsx
function Toggle() {
  const [on, setOn] = useState(false);
  return <button onClick={() => setOn(!on)}>{on ? "ON" : "OFF"}</button>;
}
```

### Props named `onSomething`

By convention, props that accept handlers start with `on` (for example `onSelect`), and the functions you write to handle them start with `handle` (`handleSelect`).

---

## 6.2 Controlled inputs

A **controlled input** is a form element whose value is owned by **React state**. The state is the single source of truth.

```jsx
import { useState } from "react";

function NameForm() {
  const [name, setName] = useState("");

  return (
    <form onSubmit={(e) => { e.preventDefault(); alert(`Hello, ${name}`); }}>
      <label>
        Name:
        <input value={name} onChange={(e) => setName(e.target.value)} />
      </label>
      <button type="submit">Greet</button>
    </form>
  );
}
```

Two props work together:

- `value={name}` makes the input display state.
- `onChange` updates state on every change.

If you set `value` without `onChange`, the input becomes read-only.

### Other input types

```jsx
// Checkbox: use `checked`
<input type="checkbox" checked={agree} onChange={(e) => setAgree(e.target.checked)} />

// Select: `value` goes on <select>
<select value={color} onChange={(e) => setColor(e.target.value)}>
  <option value="red">Red</option>
  <option value="blue">Blue</option>
</select>

// Textarea: also uses `value`
<textarea value={bio} onChange={(e) => setBio(e.target.value)} />

// Radio group: compare each radio's value to state
<input type="radio" value="small" checked={size === "small"}
       onChange={(e) => setSize(e.target.value)} />
```

### One state object for many fields

Use the input's `name` attribute to update the right field with one handler:

```jsx
function SignupForm() {
  const [form, setForm] = useState({ email: "", password: "" });

  function handleChange(e) {
    const { name, value } = e.target;
    setForm((prev) => ({ ...prev, [name]: value }));
  }

  return (
    <form>
      <input name="email" type="email" value={form.email} onChange={handleChange} />
      <input name="password" type="password" value={form.password} onChange={handleChange} />
    </form>
  );
}
```

`[name]: value` is a **computed property name**: it uses the *value* of `name` as the key.

### Why controlled?

Because the state always knows the current value, you can validate as the user types, disable the submit button, format input, or reset the form with `setForm({ email: "", password: "" })`.

---

## 6.3 Uncontrolled inputs and refs

An **uncontrolled input** keeps its own value in the DOM, like plain HTML. You read the value only when you need it (for example on submit). This means less code and fewer re-renders, but less control.

### Reading with FormData

The simplest approach is the browser's `FormData` API, using each input's `name`:

```jsx
function ContactForm() {
  function handleSubmit(event) {
    event.preventDefault();
    const formData = new FormData(event.currentTarget);
    const data = Object.fromEntries(formData);
    console.log(data);   // { email: "...", message: "..." }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input name="email" type="email" defaultValue="" />
      <textarea name="message" />
      <button type="submit">Send</button>
    </form>
  );
}
```

Use `defaultValue` (not `value`) to set an initial value on an uncontrolled input.

### Reading with a ref

A **ref** is a way to hold a reference to a DOM element. Create one with `useRef` and attach it with the `ref` prop:

```jsx
import { useRef } from "react";

function SearchBox() {
  const inputRef = useRef(null);

  function handleSearch() {
    alert(`Searching for: ${inputRef.current.value}`);
  }

  return (
    <>
      <input ref={inputRef} placeholder="Search..." />
      <button onClick={handleSearch}>Go</button>
    </>
  );
}
```

`inputRef.current` is the DOM `<input>` once the component has rendered. Refs are covered further in [Lesson 7](./[7]-More-Built-In-Hooks.md).

### File inputs are always uncontrolled

`<input type="file" />` cannot have its value set by React. Read selected files from `event.target.files`.

### Which should I use?

| Controlled | Uncontrolled |
|---|---|
| Live validation, formatting, dependent fields | Simple forms read once on submit |
| Disable buttons based on input | File inputs |
| More code, more renders | Less code |

---

## 6.4 Form validation basics

Validation has two layers: **browser validation** (built in) and **custom validation** (your code).

### Built-in HTML validation

HTML attributes such as `required`, `minLength`, `maxLength`, `pattern`, `min`, and `max`, plus input types like `email` and `number`, are checked by the browser before `onSubmit` fires:

```jsx
<input type="email" required />
<input type="password" required minLength={8} />
```

Add `noValidate` to the `<form>` if you want to turn this off and handle everything yourself.

### Custom validation with state

Keep an `errors` object in state, and fill it when validating:

```jsx
import { useState } from "react";

function validate(values) {
  const errors = {};
  if (!values.email.includes("@")) errors.email = "Enter a valid email.";
  if (values.password.length < 8) errors.password = "Use at least 8 characters.";
  return errors;
}

function SignupForm() {
  const [values, setValues] = useState({ email: "", password: "" });
  const [errors, setErrors] = useState({});
  const [submitted, setSubmitted] = useState(false);

  function handleChange(e) {
    const { name, value } = e.target;
    setValues((prev) => ({ ...prev, [name]: value }));
  }

  function handleSubmit(e) {
    e.preventDefault();
    const found = validate(values);
    setErrors(found);
    if (Object.keys(found).length === 0) setSubmitted(true);
  }

  if (submitted) return <p>Thanks for signing up!</p>;

  return (
    <form onSubmit={handleSubmit} noValidate>
      <div>
        <label htmlFor="email">Email</label>
        <input id="email" name="email" value={values.email} onChange={handleChange}
               aria-invalid={Boolean(errors.email)} />
        {errors.email && <p role="alert">{errors.email}</p>}
      </div>

      <div>
        <label htmlFor="password">Password</label>
        <input id="password" name="password" type="password"
               value={values.password} onChange={handleChange}
               aria-invalid={Boolean(errors.password)} />
        {errors.password && <p role="alert">{errors.password}</p>}
      </div>

      <button type="submit">Sign up</button>
    </form>
  );
}
```

### When to validate

- **On submit**: least annoying; always do this.
- **On blur** (leaving a field): good balance; track which fields have been "touched".
- **On every change**: instant feedback, but can show errors before the user finishes typing.

### Good practices

- Show messages **next to** the field, and make them specific.
- Connect labels with `htmlFor`/`id` so screen readers and clicks work.
- **Always validate on the server too.** Client-side checks improve usability but can be bypassed.

### Form libraries

For large forms, libraries like **React Hook Form** (with a schema validator such as **Zod**) reduce boilerplate. React 19 also adds form *Actions*, covered in [Lesson 14](./[14]-Modern-React-Features.md).

[Previous](./[5]-Effects-And-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[7]-More-Built-In-Hooks.md)