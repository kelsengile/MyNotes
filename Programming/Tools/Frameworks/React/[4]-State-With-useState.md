[Previous](./[3]-Components-And-Props.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[5]-Effects-And-Lifecycle.md)

*State And Hooks*

# Lesson 4 - State With useState

## 4.1 What state is

**State** is data a component *remembers* between renders, and that causes the component to re-render when it changes.

Why can't we just use a normal variable?

```jsx
function Counter() {
  let count = 0;                       // reset to 0 on every render
  return <button onClick={() => { count++; }}>Count: {count}</button>;
}
```

This fails for two reasons:

1. Local variables are **recreated** each time the function runs, so the value is lost.
2. Changing a local variable **does not tell React** to re-render.

The `useState` **hook** solves both:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

`useState(0)` returns an array of two items:

- `count`: the current value (initially `0`)
- `setCount`: a function that updates the value and asks React to re-render

### Rules of Hooks

Functions starting with `use` are **hooks**. They must follow two rules:

1. Call hooks only at the **top level** of a component or custom hook: not inside loops, conditions, or nested functions.
2. Call hooks only from **React function components** or other hooks.

React tracks state by the *order* in which hooks are called, so the order must be identical on every render.

### State is isolated

Each component instance has its own state. Rendering `<Counter />` twice gives two independent counters.

---

## 4.2 Updating state correctly

### State is a snapshot

Each render sees a fixed "snapshot" of state. Calling `setCount` does not change `count` in the code that is already running; it schedules a new render with the new value:

```jsx
function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1);
  // count is still the same snapshot value in all three lines,
  // so the result is count + 1, not count + 3
}
```

### Functional updates

When the new value depends on the previous value, pass an **updater function**. React queues them and runs each one in order:

```jsx
function handleClick() {
  setCount((prev) => prev + 1);
  setCount((prev) => prev + 1);
  setCount((prev) => prev + 1);
  // result: count + 3
}
```

A good habit: use the updater form whenever the next state is computed from the previous state.

### Batching

React groups multiple state updates inside one event handler into **a single re-render** for efficiency.

### Lazy initial state

If the initial value is expensive to compute, pass a function so it only runs once, on the first render:

```jsx
const [data, setData] = useState(() => loadFromLocalStorage());
```

Note this is `() => loadFromLocalStorage()`, not `loadFromLocalStorage()`. The latter would run on every render.

### Same value, no re-render

If you set state to the same value it already has (compared with `Object.is`), React skips re-rendering.

---

## 4.3 Objects, arrays, and immutability

Treat state as **read-only**. To change an object or array, create a **new copy** with the change applied. This is called **immutable** updating. React detects changes by checking if the reference is new.

### Objects

```jsx
const [user, setUser] = useState({ name: "Ada", city: "London" });

// Wrong: mutates the existing object. React may not re-render.
user.city = "Paris";

// Right: new object, copying old fields with spread, then overriding
setUser({ ...user, city: "Paris" });
// or, using the previous value
setUser((prev) => ({ ...prev, city: "Paris" }));
```

For nested objects, copy each level you change:

```jsx
setUser((prev) => ({
  ...prev,
  address: { ...prev.address, city: "Paris" },
}));
```

### Arrays

Avoid mutating methods (`push`, `pop`, `splice`, `sort`, `reverse`, direct index assignment). Use methods that return new arrays:

| Goal | Avoid | Use |
|---|---|---|
| Add | `push`, `unshift` | `[...items, newItem]` |
| Remove | `splice` | `items.filter(i => i.id !== id)` |
| Replace/update | `items[0] = x` | `items.map(i => i.id === id ? {...i, done: true} : i)` |
| Sort | `items.sort()` | `[...items].sort(compareFn)` |

A complete example:

```jsx
import { useState } from "react";

function TodoList() {
  const [todos, setTodos] = useState([
    { id: 1, text: "Learn state", done: false },
  ]);
  const [text, setText] = useState("");

  function addTodo() {
    if (!text.trim()) return;
    setTodos((prev) => [...prev, { id: Date.now(), text, done: false }]);
    setText("");
  }

  function toggle(id) {
    setTodos((prev) =>
      prev.map((t) => (t.id === id ? { ...t, done: !t.done } : t))
    );
  }

  function remove(id) {
    setTodos((prev) => prev.filter((t) => t.id !== id));
  }

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={addTodo}>Add</button>
      <ul>
        {todos.map((t) => (
          <li key={t.id}>
            <label>
              <input
                type="checkbox"
                checked={t.done}
                onChange={() => toggle(t.id)}
              />
              <span style={{ textDecoration: t.done ? "line-through" : "none" }}>
                {t.text}
              </span>
            </label>
            <button onClick={() => remove(t.id)}>Delete</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

For deeply nested state, libraries like **Immer** let you write "mutating-looking" code that produces immutable updates.

---

## 4.4 Lifting state up

When two components need to **share or stay in sync with the same data**, move the state to their **closest common parent** and pass it down as props. This is called *lifting state up*.

### Example: two inputs in sync

```jsx
import { useState } from "react";

function TemperatureInput({ label, value, onChange }) {
  return (
    <label>
      {label}:{" "}
      <input
        type="number"
        value={value}
        onChange={(e) => onChange(e.target.value)}
      />
    </label>
  );
}

function Converter() {
  const [celsius, setCelsius] = useState("");

  const fahrenheit =
    celsius === "" ? "" : String((Number(celsius) * 9) / 5 + 32);

  function handleFahrenheitChange(value) {
    setCelsius(value === "" ? "" : String(((Number(value) - 32) * 5) / 9));
  }

  return (
    <div>
      <TemperatureInput label="Celsius" value={celsius} onChange={setCelsius} />
      <TemperatureInput
        label="Fahrenheit"
        value={fahrenheit}
        onChange={handleFahrenheitChange}
      />
    </div>
  );
}
```

### The pattern

1. **Identify** the components that need the data.
2. **Find** their closest common parent.
3. **Store** the state in that parent.
4. **Pass data down** as props, and **pass setter functions down** as props (callbacks) so children can request changes.

### Single source of truth

Keep one copy of each piece of data. Notice `fahrenheit` above is **derived** from `celsius` during render instead of stored in a second state. Do not store in state what you can calculate from existing state or