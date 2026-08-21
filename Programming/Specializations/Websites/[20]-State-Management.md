[Previous](./[19]-Client-Side-Routing.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[21]-Servers-and-Node.md)

*Front-End Frameworks*

# Lesson 20 - State Management (Context, Redux)

## 20.1 The "Prop Drilling" Problem

`useState` (Lesson 18) works well for state a single component owns. But when many components, scattered across the tree, need access to the same data (the logged-in user, a shopping cart, a theme setting), passing it down through props at every level — **prop drilling** — becomes tedious and fragile, especially when intermediate components don't need the data themselves but must still pass it through.

---

## 20.2 React Context

**Context** lets you share a value across a component tree without manually passing it through every level:

```jsx
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar() {
  const theme = useContext(ThemeContext); // "dark", no props needed
  return <div className={theme}>...</div>;
}
```

Context is well suited to relatively stable, infrequently-changing data — themes, current user, locale — but isn't necessarily the best tool for state that changes rapidly across a large app, since every consumer re-renders when the context value changes.

---

## 20.3 When You Need More: External State Libraries

For larger applications with complex, frequently-updated shared state, dedicated state management libraries offer more structure and better performance characteristics. **Redux** is the most established example: state lives in a single central store, and the only way to change it is by dispatching a plain object called an **action**, handled by a **reducer** function:

```js
function counterReducer(state = { count: 0 }, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    default:
      return state;
  }
}
```

This strict, predictable data flow makes large apps easier to debug — every state change traces back to a specific dispatched action.

---

## 20.4 Lighter Alternatives

Redux's ceremony isn't always necessary. Libraries like **Zustand** or **Jotai** offer similar shared-state capabilities with far less boilerplate, and are increasingly popular for apps that want more structure than Context but less overhead than Redux. There's no single correct choice — the right tool depends on how complex and how frequently-changing the shared state actually is.

---

## 20.5 Server State vs Client State

Not all state is the same kind of problem. **Client state** (a modal being open, form input) lives only in the browser. **Server state** (data fetched from an API) is a cached copy of something that lives elsewhere and can go stale. Libraries like **TanStack Query** specialize specifically in managing server state — caching, refetching, and synchronizing — which is a different problem than the client-state tools above solve, and combining both kinds of tools appropriately is common in real applications.

---

[Previous](./[19]-Client-Side-Routing.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[21]-Servers-and-Node.md)
