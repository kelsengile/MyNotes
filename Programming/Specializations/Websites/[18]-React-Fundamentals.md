[Previous](./[17]-Component-Based-UI.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[19]-Client-Side-Routing.md)

*Front-End Frameworks*

# Lesson 18 - React Fundamentals (Components, Props, State)

## 18.1 Your First Component

A React component is typically a function that returns **JSX** — a syntax extension that looks like HTML but is actually JavaScript:

```jsx
function Greeting() {
  return <h1>Hello, world!</h1>;
}
```

Under the hood, JSX compiles to plain JavaScript function calls; the build tooling from Lesson 13 handles this transformation automatically.

---

## 18.2 Props

**Props** (short for properties) pass data from a parent component into a child, similar to function arguments:

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

function App() {
  return <Greeting name="Ada" />;
}
```

Props are read-only from the child's perspective — a component should never modify its own props directly.

---

## 18.3 State with useState

**State** is data a component owns and can change over time, triggering a re-render whenever it updates. The `useState` **Hook** is how function components manage state:

```jsx
import { useState } from "react";

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

`useState(0)` returns the current value and a setter function; calling the setter schedules a re-render with the new value.

---

## 18.4 Handling Events

React events are named in camelCase and passed as functions, not strings:

```jsx
function Form() {
  function handleSubmit(event) {
    event.preventDefault();
    console.log("Submitted!");
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Send</button>
    </form>
  );
}
```

---

## 18.5 useEffect and Side Effects

Some work — fetching data, subscribing to an event, setting a timer — needs to happen outside the normal render flow. The `useEffect` Hook runs code after render, and optionally cleans up after itself:

```jsx
useEffect(() => {
  const timer = setInterval(() => console.log("tick"), 1000);
  return () => clearInterval(timer); // cleanup
}, []); // empty array = run once, on mount
```

The dependency array (the second argument) controls when the effect re-runs — omitting it runs on every render, an empty array runs once, and listing values re-runs the effect whenever any of them change.

---

## 18.6 Composing Components

Real UIs are built by nesting components and passing data down through props, forming the same kind of tree described in Lesson 17:

```jsx
function App() {
  return (
    <div>
      <Header />
      <UserList users={users} />
      <Footer />
    </div>
  );
}
```

---

[Previous](./[17]-Component-Based-UI.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[19]-Client-Side-Routing.md)
