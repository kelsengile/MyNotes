[Previous](./[4]-State-With-useState.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[6]-Handling-Events-And-Forms.md)

*State And Hooks*

# Lesson 5 - Effects And Lifecycle

## 5.1 Side effects explained

Rendering must be **pure**: it only calculates JSX from props and state. But real apps must also interact with things *outside* React. These are **side effects**:

- fetching data from a server
- setting up a subscription (WebSocket, event listener)
- starting timers
- changing `document.title`
- using browser APIs (`localStorage`, `IntersectionObserver`)
- controlling non-React widgets (maps, charts)

### Events vs effects

There are two places for side effects:

| Where | When it runs | Example |
|---|---|---|
| **Event handler** | In response to a user action | Submit a form, then `POST` data |
| **Effect (`useEffect`)** | Because the component was *displayed* or its data changed | Connect to a chat room when the room page appears |

Always prefer event handlers when the side effect is caused by a specific interaction. Use an effect when the side effect needs to **synchronize** the component with an external system.

### The component lifecycle

A component goes through three phases:

1. **Mount**: it appears on the screen for the first time.
2. **Update**: it re-renders because props or state changed (can happen many times).
3. **Unmount**: it is removed from the screen.

Effects let you run code at these moments, but the better mental model is: *"an effect describes how to start synchronizing with something, and how to stop"*, which can happen many times over a component's life.

---

## 5.2 useEffect and dependency arrays

```jsx
import { useEffect, useState } from "react";

function DocumentTitle() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Clicked ${count} times`;
  }, [count]);

  return <button onClick={() => setCount(count + 1)}>Click</button>;
}
```

`useEffect(setup, dependencies)`:

- **setup**: a function containing the side effect.
- **dependencies**: an array of every reactive value (props, state, variables declared in the component) the setup function reads.

React runs the effect **after the component renders and the screen updates**.

### The three forms of the dependency array

```jsx
useEffect(() => { /* ... */ });          // No array: runs after EVERY render
useEffect(() => { /* ... */ }, []);      // Empty array: runs once, after the first render
useEffect(() => { /* ... */ }, [a, b]);  // Runs after the first render and whenever a or b changed
```

React compares dependencies using `Object.is`. A new object or array created during render counts as "changed" every time.

### Fetching data on mount

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    let ignore = false;

    async function load() {
      const res = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
      const data = await res.json();
      if (!ignore) setUser(data);
    }

    load();

    return () => {
      ignore = true;      // cleanup, explained in 5.3
    };
  }, [userId]);

  if (!user) return <p>Loading...</p>;
  return <h2>{user.name}</h2>;
}
```

The effect function itself cannot be `async` (it must return either nothing or a cleanup function, not a Promise), so define an async function inside and call it.

### Don't lie about dependencies

The linter rule `react-hooks/exhaustive-deps` warns when a value is used in the effect but missing from the array. Do not silence it. Instead, fix the code by moving the value inside the effect, moving it outside the component, or restructuring.

---

## 5.3 Cleanup functions

An effect can return a **cleanup function**. React runs it:

- **before the effect runs again** (because dependencies changed), and
- **when the component unmounts**.

### Event listener

```jsx
useEffect(() => {
  function handleResize() {
    setWidth(window.innerWidth);
  }
  window.addEventListener("resize", handleResize);

  return () => {
    window.removeEventListener("resize", handleResize);
  };
}, []);
```

### Timer

```jsx
function Clock() {
  const [time, setTime] = useState(() => new Date());

  useEffect(() => {
    const id = setInterval(() => setTime(new Date()), 1000);
    return () => clearInterval(id);
  }, []);

  return <p>{time.toLocaleTimeString()}</p>;
}
```

### Connection

```jsx
useEffect(() => {
  const connection = createConnection(roomId);
  connection.connect();
  return () => connection.disconnect();
}, [roomId]);
```

(`createConnection` here stands for any function that opens a connection; it is a placeholder.)

When `roomId` changes, React disconnects from the old room first, then connects to the new one.

### Why StrictMode runs effects twice

In development, `StrictMode` deliberately runs **setup → cleanup → setup** once when a component mounts. This is a test: if your cleanup correctly undoes your setup, the double run is invisible. If you see duplicate subscriptions or requests in development, your cleanup is probably missing or incomplete. This double run does **not** happen in production.

### Cancelling requests

Use `AbortController` to cancel a fetch on cleanup:

```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal })
    .then((res) => res.json())
    .then(setData)
    .catch((err) => {
      if (err.name !== "AbortError") setError(err);
    });

  return () => controller.abort();
}, [url]);
```

---

## 5.4 Common effect mistakes

### Mistake 1: Infinite loops

Updating state inside an effect that depends on that state:

```jsx
// Bad: runs after every render, sets state, causes a render, repeats
useEffect(() => {
  setCount(count + 1);
});
```

Always check what triggers the effect, and use a dependency array.

### Mistake 2: Objects and functions as dependencies

```jsx
function Chat({ roomId }) {
  const options = { roomId };            // new object every render

  useEffect(() => {
    const conn = createConnection(options);
    conn.connect();
    return () => conn.disconnect();
  }, [options]);                          // "changed" every render: reconnects constantly
}
```

Fix: create the object **inside** the effect and depend on the primitive (`roomId`).

### Mistake 3: Using an effect to calculate data

```jsx
// Unnecessary effect
const [fullName, setFullName] = useState("");
useEffect(() => {
  setFullName(firstName + " " + lastName);
}, [firstName, lastName]);

// Better: compute during render
const fullName = firstName + " " + lastName;
```

If you can calculate something from props or state, do it during rendering. It is simpler and avoids an extra render.

### Mistake 4: Using an effect to respond to user events

If something happens *because the user clicked*, do it in the click handler, not in an effect that watches state set by that click.

### Mistake 5: Resetting state when a prop changes

Instead of an effect that resets state when `userId` changes, give the component a **`key`** so React treats it as a new instance:

```jsx
<Profile userId={userId} key={userId} />
```

### Mistake 6: Race conditions in fetching

If `userId` changes quickly, an older request may finish *after* a newer one and overwrite the right data. The `ignore` flag or `AbortController` from earlier solves this. In real apps a data library (see [Lesson 10](./[10]-Data-Fetching-And-APIs.md)) usually handles it for you.

### Summary checklist

- Do I really need an effect, or is this an event handler or derived value?
- Does the effect list **all** its reactive values?
- Does it clean up everything it starts?
- Does it behave correctly if run twice in development?

[Previous](./[4]-State-With-useState.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[6]-Handling-Events-And-Forms.md)