[Previous](./[6]-Handling-Events-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[8]-Context-And-Global-State.md)

*State And Hooks*

# Lesson 7 - More Built-In Hooks

## 7.1 useRef and the DOM

`useRef` returns a plain object `{ current: initialValue }` that **persists across renders**. Changing `ref.current` does **not** trigger a re-render.

```jsx
const ref = useRef(0);
ref.current = ref.current + 1;   // no re-render
```

There are two main uses.

### Use 1: Accessing DOM elements

Attach a ref to an element with the `ref` prop. React sets `ref.current` to the DOM node after rendering.

```jsx
import { useRef, useEffect } from "react";

function AutoFocusInput() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} />;
}
```

Typical DOM tasks: focusing, scrolling into view, measuring size, playing/pausing media.

```jsx
function Video({ src }) {
  const videoRef = useRef(null);
  return (
    <>
      <video ref={videoRef} src={src} />
      <button onClick={() => videoRef.current.play()}>Play</button>
      <button onClick={() => videoRef.current.pause()}>Pause</button>
    </>
  );
}
```

### Use 2: Storing mutable values that aren't displayed

Refs are useful for values that must survive re-renders but don't affect the output, such as timer IDs or the previous value of something:

```jsx
function Stopwatch() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef(null);

  function start() {
    if (intervalRef.current) return;
    intervalRef.current = setInterval(() => setSeconds((s) => s + 1), 1000);
  }

  function stop() {
    clearInterval(intervalRef.current);
    intervalRef.current = null;
  }

  return (
    <div>
      <p>{seconds}s</p>
      <button onClick={start}>Start</button>
      <button onClick={stop}>Stop</button>
    </div>
  );
}
```

### Rules

- Don't read or write `ref.current` **during rendering** (except for initialization); do it in event handlers or effects.
- If a value should appear on screen, use **state**, not a ref.

### Refs as props in React 19

In React 19, function components can receive `ref` as a normal prop, so `forwardRef` is no longer needed for new code:

```jsx
function MyInput({ ref, ...props }) {
  return <input ref={ref} {...props} />;
}
```

In React 18 and earlier, you must wrap the component with `forwardRef`.

---

## 7.2 useMemo and useCallback

These hooks **cache** a value or function between renders so it is only recalculated when dependencies change. They are performance tools; your code must work correctly without them.

### useMemo: cache a calculated value

```jsx
import { useMemo, useState } from "react";

function ProductList({ products }) {
  const [query, setQuery] = useState("");

  const visible = useMemo(() => {
    return products.filter((p) =>
      p.name.toLowerCase().includes(query.toLowerCase())
    );
  }, [products, query]);

  return (
    <>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <ul>
        {visible.map((p) => <li key={p.id}>{p.name}</li>)}
      </ul>
    </>
  );
}
```

`visible` is only recomputed when `products` or `query` change. If some other state changes and the component re-renders, the cached result is reused.

### useCallback: cache a function

`useCallback(fn, deps)` returns the **same function reference** between renders until a dependency changes:

```jsx
const handleSelect = useCallback((id) => {
  setSelectedId(id);
}, []);
```

Why would function identity matter? Two reasons:

1. The function is passed to a child wrapped in `React.memo` (see [Lesson 13](./[13]-Performance-Optimization.md)). A new function each render would make the memo useless.
2. The function is a dependency of `useEffect` or another hook.

### When not to use them

Don't wrap everything. Memoization has its own cost, and most calculations are fast. Add them when:

- you've **measured** a real slowness, or
- you need a stable reference as described above.

> The **React Compiler** (see [Lesson 14](./[14]-Modern-React-Features.md)) can add this kind of memoization automatically, so manual `useMemo`/`useCallback` becomes less necessary in new code.

---

## 7.3 useReducer for complex state

`useReducer` manages state through a **reducer**: a pure function `(state, action) => newState`. It suits state with many related fields or many kinds of updates.

```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

- `state`: the current state.
- `dispatch(action)`: sends an **action** (an object describing what happened).
- `reducer`: decides the next state from the current state and the action.

### Example: shopping cart

```jsx
import { useReducer } from "react";

const initialState = { items: [] };

function cartReducer(state, action) {
  switch (action.type) {
    case "added": {
      const existing = state.items.find((i) => i.id === action.item.id);
      if (existing) {
        return {
          items: state.items.map((i) =>
            i.id === action.item.id ? { ...i, qty: i.qty + 1 } : i
          ),
        };
      }
      return { items: [...state.items, { ...action.item, qty: 1 }] };
    }
    case "removed":
      return { items: state.items.filter((i) => i.id !== action.id) };
    case "cleared":
      return initialState;
    default:
      throw new Error(`Unknown action: ${action.type}`);
  }
}

function Cart() {
  const [state, dispatch] = useReducer(cartReducer, initialState);

  return (
    <div>
      <button onClick={() => dispatch({ type: "added", item: { id: 1, name: "Pen" } })}>
        Add pen
      </button>
      <button onClick={() => dispatch({ type: "cleared" })}>Clear</button>
      <ul>
        {state.items.map((i) => (
          <li key={i.id}>
            {i.name} x{i.qty}{" "}
            <button onClick={() => dispatch({ type: "removed", id: i.id })}>Remove</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### useState vs useReducer

| `useState` | `useReducer` |
|---|---|
| Simple, independent values | Several values that change together |
| Few ways to update | Many kinds of updates |
| Less code up front | All update logic in one testable place |

Reducers must be **pure** (no side effects, no mutation), so they are easy to unit test as plain functions.

---

## 7.4 Writing custom hooks

A **custom hook** is a function whose name starts with `use` and that calls other hooks. It lets you **reuse stateful logic** (not UI) across components.

### Example: useToggle

```jsx
import { useState, useCallback } from "react";

function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = useCallback(() => setValue((v) => !v), []);
  return [value, toggle];
}

function Menu() {
  const [open, toggleOpen] = useToggle();
  return (
    <>
      <button onClick={toggleOpen}>Menu</button>
      {open && <nav>...</nav>}
    </>
  );
}
```

### Example: useLocalStorage

```jsx
import { useState, useEffect } from "react";

function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored !== null ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

function ThemePicker() {
  const [theme, setTheme] = useLocalStorage("theme", "light");
  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      Current: {theme}
    </button>
  );
}
```

### Example: useFetch

```jsx
import { useState, useEffect } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const controller = new AbortController();
    setLoading(true);
    setError(null);

    fetch(url, { signal: controller.signal })
      .then((res) => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(setData)
      .catch((err) => {
        if (err.name !== "AbortError") setError(err);
      })
      .finally(() => {
        if (!controller.signal.aborted) setLoading(false);
      });

    return () => controller.abort();
  }, [url]);

  return { data, error, loading };
}
```

### Key points

- The `use` prefix is required: it lets React and the linter enforce the Rules of Hooks.
- Each call to a custom hook gets **its own isolated state**. Two components using `useToggle` do not share a value.
- Custom hooks share **logic**, not state. To share state itself, use Context (next lesson) or a state library.
- Name hooks after what they do (`useOnlineStatus`), and keep them focused on one job.

[Previous](./[6]-Handling-Events-And-Forms.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[8]-Context-And-Global-State.md)