[Previous](./[12]-Project-Structure-And-Tooling.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[14]-Modern-React-Features.md)

*Going Further*

# Lesson 13 - Performance Optimization

## 13.1 How rendering works

Understanding **when** React renders is the key to understanding performance.

### The three phases

1. **Trigger:** something requests a render: the first render, or a state update (`setState`).
2. **Render:** React calls your component functions to calculate the new JSX. This is pure calculation, not DOM updates.
3. **Commit:** React applies the necessary differences to the real DOM. If nothing changed, it touches nothing.

Note that "rendering" in React means **calling your component function**. A component can re-render without the DOM changing at all.

### What causes a component to re-render?

- Its **own state** changes.
- Its **parent re-renders** (by default, all children re-render too, even if their props are identical).
- A **context** it reads changes.

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>{count}</button>
      <ExpensiveChild />   {/* re-renders on every click, though it uses no props */}
    </>
  );
}
```

### Rendering is usually fast

Re-renders are normal and most are cheap. Do not optimize without evidence. The order of operations should be:

1. Write clear code.
2. **Measure** (13.4) when something feels slow.
3. Fix the biggest bottleneck.

### Techniques that cost nothing

Before reaching for memoization, try restructuring:

**Move state down** so only the component that needs it re-renders:

```jsx
// Before: typing re-renders the whole Page
function Page() {
  const [text, setText] = useState("");
  return (
    <>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <HeavyChart />
    </>
  );
}

// After: only SearchBox re-renders
function SearchBox() {
  const [text, setText] = useState("");
  return <input value={text} onChange={(e) => setText(e.target.value)} />;
}

function Page() {
  return (
    <>
      <SearchBox />
      <HeavyChart />
    </>
  );
}
```

**Pass components as `children`:** content passed as `children` is created by the parent of the stateful component, so it does not re-render when that component's state changes.

---

## 13.2 React.memo and memoization

### React.memo

`React.memo` wraps a component so React **skips re-rendering it when its props haven't changed** (compared shallowly).

```jsx
import { memo } from "react";

const ExpensiveChild = memo(function ExpensiveChild({ items }) {
  console.log("ExpensiveChild rendered");
  return <ul>{items.map((i) => <li key={i.id}>{i.name}</li>)}</ul>;
});
```

### The reference equality trap

`memo` compares props with `Object.is`. New objects, arrays, or functions created in the parent on every render look "different" each time, so memo is defeated:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const style = { color: "red" };            // new object every render
  const handleClick = () => {};               // new function every render

  return <MemoChild style={style} onClick={handleClick} />;  // re-renders anyway
}
```

Fix by stabilizing the references:

```jsx
function Parent() {
  const [count, setCount] = useState(0);

  const style = useMemo(() => ({ color: "red" }), []);
  const handleClick = useCallback(() => {}, []);

  return <MemoChild style={style} onClick={handleClick} />;
}
```

(Moving constants **outside** the component also works, as they are created only once.)

### A complete memoization toolbox

| Tool | Caches | Use for |
|---|---|---|
| `React.memo` | A component's rendered output | Skipping re-renders of a child with unchanged props |
| `useMemo` | A calculated value | Expensive calculations, stable object/array references |
| `useCallback` | A function | Stable function references for memoized children or effect dependencies |

### When to use memoization

- A component re-renders often and its rendering is **measurably slow** (large lists, charts, complex trees).
- The props passed to it are stable or can be made stable.

### When not to

- Simple, fast components: the comparison can cost as much as re-rendering.
- Props that change on nearly every render anyway.
- "Just in case" everywhere: it adds complexity.

> With the **React Compiler** ([Lesson 14](./[14]-Modern-React-Features.md)), much of this memoization is applied automatically at build time.

### Lists

For very long lists (thousands of rows), memoization isn't enough, since the DOM itself gets too large. Use **virtualization** (windowing) with libraries like **TanStack Virtual** or **react-window**, which render only the rows currently visible.

---

## 13.3 Code splitting and lazy loading

By default a bundler puts all your code in one large JavaScript file. Users must download everything before the app starts, including pages they may never visit. **Code splitting** divides the bundle into smaller **chunks** loaded on demand.

### React.lazy and Suspense

`lazy` loads a component only when it is first rendered. It takes a function returning a dynamic `import()`:

```jsx
import { lazy, Suspense } from "react";

const Dashboard = lazy(() => import("./pages/Dashboard.jsx"));

function App() {
  return (
    <Suspense fallback={<p>Loading page...</p>}>
      <Dashboard />
    </Suspense>
  );
}
```

Requirements:

- The imported module must have a **default export** that is the component.
- `lazy()` must be called at the **top level** of a module, not inside a component.
- A `Suspense` boundary above it supplies the loading fallback.

### Route-based splitting

The most effective place to split is at **routes**, so each page is its own chunk:

```jsx
const Home = lazy(() => import("./pages/Home.jsx"));
const Settings = lazy(() => import("./pages/Settings.jsx"));

const router = createBrowserRouter([
  {
    path: "/",
    element: (
      <Suspense fallback={<p>Loading...</p>}>
        <Home />
      </Suspense>
    ),
  },
  {
    path: "/settings",
    element: (
      <Suspense fallback={<p>Loading...</p>}>
        <Settings />
      </Suspense>
    ),
  },
]);
```

### Splitting heavy components

Large, rarely-used pieces (rich text editors, chart libraries, modals) are good candidates:

```jsx
const Editor = lazy(() => import("./Editor.jsx"));

function Post() {
  const [editing, setEditing] = useState(false);
  return (
    <>
      <button onClick={() => setEditing(true)}>Edit</button>
      {editing && (
        <Suspense fallback={<p>Loading editor...</p>}>
          <Editor />
        </Suspense>
      )}
    </>
  );
}
```

### Other loading wins

- **Image optimization:** correct sizes, modern formats (WebP/AVIF), `loading="lazy"` on offscreen images.
- **Dependency size:** check what you import. Prefer smaller libraries or importing only parts you need.
- **Analyze the bundle** with a tool such as `rollup-plugin-visualizer` to see what takes up space.

---

## 13.4 Profiling with DevTools

### React Developer Tools

Install the **React Developer Tools** browser extension (Chrome, Firefox, Edge). It adds two tabs to the browser's DevTools:

- **Components:** inspect the component tree, view and edit props and state.
- **Profiler:** record interactions and see which components rendered and how long they took.

### Using the Profiler

1. Open the **Profiler** tab and click **Record**.
2. Perform the slow action in your app (type, click, scroll).
3. Click **Stop**.
4. Study the results:
   - The **flamegraph** shows the tree, with each bar's width representing render time and color showing relative cost. Grey means it did not render.
   - The **ranked** view lists components from slowest to fastest.
   - Select a component to see **why it rendered** (props changed, state changed, parent rendered). You may need to enable "Record why each component rendered while profiling" in the Profiler settings.

### Useful DevTools settings

- **"Highlight updates when components render"** (in the Components tab settings) flashes a border around each component as it re-renders. It's a quick way to spot unnecessary re-renders.

### Profile production-like builds

Development mode is slower because of `StrictMode` and extra checks. For realistic timings, run `npm run build` then `npm run preview`.

### The `<Profiler>` component

You can also measure in code:

```jsx
import { Profiler } from "react";

function onRender(id, phase, actualDuration) {
  console.log(`${id} (${phase}): ${actualDuration.toFixed(1)}ms`);
}

<Profiler id="ProductList" onRender={onRender}>
  <ProductList />
</Profiler>
```

### Beyond React

Use the browser's **Performance** tab and **Lighthouse** to measure loading, layout, and network issues, which are often more significant than rendering cost.

### The optimization loop

1. **Measure** to find the real bottleneck.
2. **Change** one thing (restructure, memoize, split, virtualize).
3. **Measure again** to confirm it helped.

[Previous](./[12]-Project-Structure-And-Tooling.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[14]-Modern-React-Features.md)