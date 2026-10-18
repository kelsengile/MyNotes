[Previous](./[7]-More-Built-In-Hooks.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[9]-Routing-With-React-Router.md)

*State And Hooks*

# Lesson 8 - Context And Global State

## 8.1 Prop drilling problem

**Prop drilling** is passing props through many layers of components that don't use them, only to deliver data to a deeply nested component.

```jsx
function App() {
  const [user, setUser] = useState({ name: "Ada" });
  return <Layout user={user} />;
}

function Layout({ user }) {          // doesn't use user
  return <Sidebar user={user} />;
}

function Sidebar({ user }) {         // doesn't use user
  return <Avatar user={user} />;
}

function Avatar({ user }) {          // finally uses it
  return <span>{user.name}</span>;
}
```

This is manageable for two layers but becomes tedious and fragile for five. Every middle component must know about props it doesn't care about.

Before reaching for global state, try these simpler fixes:

1. **Restructure with composition.** Pass JSX through `children` or slot props so intermediate components don't need the data (see [Lesson 3](./[3]-Components-And-Props.md)).
2. **Keep state close** to where it is used, and lift it only as far as needed.

If many distant components truly need the same data (current user, theme, language), use **Context** or a **state library**.

---

## 8.2 The Context API

**Context** lets a parent make a value available to **any component below it**, without passing props.

Three steps: **create**, **provide**, **consume**.

### Step 1: Create

```jsx
// ThemeContext.jsx
import { createContext } from "react";

export const ThemeContext = createContext("light");   // "light" = default value
```

### Step 2: Provide

Wrap part of the tree in the context, with a `value`. In **React 19** the context object itself is the provider:

```jsx
import { useState } from "react";
import { ThemeContext } from "./ThemeContext.jsx";

function App() {
  const [theme, setTheme] = useState("light");

  return (
    <ThemeContext value={{ theme, setTheme }}>
      <Page />
    </ThemeContext>
  );
}
```

In React 18 and earlier, use `<ThemeContext.Provider value={...}>` instead. `.Provider` still works in React 19.

### Step 3: Consume

Any descendant reads the value with `useContext`:

```jsx
import { useContext } from "react";
import { ThemeContext } from "./ThemeContext.jsx";

function ThemeButton() {
  const { theme, setTheme } = useContext(ThemeContext);
  return (
    <button onClick={() => setTheme(theme === "light" ? "dark" : "light")}>
      Theme: {theme}
    </button>
  );
}
```

React 19 also provides `use(ThemeContext)`, which reads context like `useContext` but can additionally be called conditionally.

### A cleaner pattern: provider component + custom hook

```jsx
// ThemeProvider.jsx
import { createContext, useContext, useState } from "react";

const ThemeContext = createContext(null);

export function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");
  return (
    <ThemeContext value={{ theme, setTheme }}>
      {children}
    </ThemeContext>
  );
}

export function useTheme() {
  const ctx = useContext(ThemeContext);
  if (ctx === null) {
    throw new Error("useTheme must be used inside <ThemeProvider>");
  }
  return ctx;
}
```

Use it in `main.jsx`:

```jsx
<ThemeProvider>
  <App />
</ThemeProvider>
```

And in any component: `const { theme } = useTheme();`

### Things to know

- When a context value changes, **every component that reads it re-renders**.
- Passing a new object (`value={{ theme, setTheme }}`) each render changes the value every time the provider re-renders. For large trees, store the value in `useMemo`, or split into separate contexts (one for data, one for actions).
- Context is **not** a replacement for all state. It is best for values that change **rarely**: theme, locale, authenticated user.
- Contexts can be nested; a component reads the **closest** provider above it.

---

## 8.3 Redux Toolkit and Zustand

For frequently changing or complex application-wide state, dedicated libraries offer more power: selective subscriptions (only components using changed data re-render), dev tools, and structure.

### Redux Toolkit

**Redux Toolkit (RTK)** is the official, recommended way to use Redux.

```bash
npm install @reduxjs/toolkit react-redux
```

**Create a slice** (state + the actions that change it). RTK uses Immer, so "mutating" code in reducers is safe:

```jsx
// counterSlice.js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: { value: 0 },
  reducers: {
    increment: (state) => { state.value += 1; },
    decrement: (state) => { state.value -= 1; },
    addBy: (state, action) => { state.value += action.payload; },
  },
});

export const { increment, decrement, addBy } = counterSlice.actions;
export default counterSlice.reducer;
```

**Create the store:**

```jsx
// store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice.js";

export const store = configureStore({
  reducer: { counter: counterReducer },
});
```

**Provide it and use it:**

```jsx
// main.jsx
import { Provider } from "react-redux";
import { store } from "./store.js";

createRoot(document.getElementById("root")).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

```jsx
// Counter.jsx
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement } from "./counterSlice.js";

function Counter() {
  const value = useSelector((state) => state.counter.value);
  const dispatch = useDispatch();

  return (
    <div>
      <button onClick={() => dispatch(decrement())}>-</button>
      <span>{value}</span>
      <button onClick={() => dispatch(increment())}>+</button>
    </div>
  );
}
```

`useSelector` subscribes the component to only the slice of state it selects.

### Zustand

**Zustand** is a small, minimal-boilerplate store. No provider is needed.

```bash
npm install zustand
```

```jsx
// useCartStore.js
import { create } from "zustand";

export const useCartStore = create((set) => ({
  items: [],
  addItem: (item) => set((state) => ({ items: [...state.items, item] })),
  clear: () => set({ items: [] }),
}));
```

```jsx
// Anywhere in your app
import { useCartStore } from "./useCartStore.js";

function CartBadge() {
  const count = useCartStore((state) => state.items.length);
  return <span>Cart ({count})</span>;
}

function AddButton({ product }) {
  const addItem = useCartStore((state) => state.addItem);
  return <button onClick={() => addItem(product)}>Add</button>;
}
```

Selecting only what you need (`state.items.length`) means the component re-renders only when that value changes.

---

## 8.4 Choosing a state approach

Not all state is the same. First decide **what kind of state** you have:

| Kind of state | Examples | Good tool |
|---|---|---|
| **Local UI state** | Is a dropdown open? Input text | `useState` / `useReducer` |
| **Shared by a few nearby components** | Selected tab, filter | Lift state up |
| **Rarely changing global values** | Theme, locale, current user | Context |
| **Complex or frequently changing client state** | Shopping cart, editor state, large app settings | Zustand or Redux Toolkit |
| **Server data** (from an API) | Product lists, user profiles | TanStack Query (see [Lesson 10](./[10]-Data-Fetching-And-APIs.md)) |
| **URL state** | Current page, search query, filters | The router (see [Lesson 9](./[9]-Routing-With-React-Router.md)) |
| **Form state** | Field values, errors | Local state or a form library |

### A practical decision path

1. **Start local.** Use `useState` in the component that needs the value.
2. **Lift up** only when siblings need the same data.
3. **Use Context** when the value is needed far away and changes rarely.
4. **Use a library** when state is shared widely, updates often, or logic gets complex.
5. **Do not copy server data into global state.** Let a data-fetching library own it, since it already handles caching, refetching, and loading states.

### Redux Toolkit vs Zustand

| | Redux Toolkit | Zustand |
|---|---|---|
| Boilerplate | More (slices, store, provider) | Very little |
| Structure | Strong conventions, good for large teams | Flexible |
| Dev tooling | Excellent Redux DevTools support | Supports DevTools via middleware |
| Provider needed | Yes | No |
| Learning curve | Steeper | Gentle |

Both are solid. Pick the one that fits your team's size and needs, and avoid adding either until simpler tools stop being enough.

[Previous](./[7]-More-Built-In-Hooks.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[9]-Routing-With-React-Router.md)