[Previous](./[2]-JSX-And-Elements.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[4]-State-With-useState.md)

*Basics*

# Lesson 3 - Components And Props

## 3.1 Function components

Modern React components are plain JavaScript **functions** that return JSX.

```jsx
function Welcome() {
  return <h1>Welcome to the site</h1>;
}

export default Welcome;
```

Rules for components:

- **Names start with a capital letter.** `<Welcome />` is a component; `<welcome />` is treated as an HTML tag.
- **They return renderable output**: JSX, a string, a number, `null` (render nothing), or an array.
- **They must be pure** during rendering: given the same props and state, they return the same JSX, and they do not change things outside themselves (no editing global variables or the DOM while rendering).
- **Define them at the top level** of a file. Do not define a component inside another component, or it is recreated on every render and loses its state.

### Using a component

Use a component like an HTML tag. Components can be reused any number of times:

```jsx
import Welcome from "./Welcome.jsx";

function App() {
  return (
    <>
      <Welcome />
      <Welcome />
    </>
  );
}
```

### Export styles

```jsx
// Default export: one per file, imported with any name
export default function Welcome() { /* ... */ }
import Welcome from "./Welcome.jsx";

// Named export: many per file, imported with exact name in braces
export function Welcome() { /* ... */ }
import { Welcome } from "./Welcome.jsx";
```

---

## 3.2 Passing and reading props

**Props** (short for properties) are the inputs to a component. They work like HTML attributes, but can hold any JavaScript value: strings, numbers, arrays, objects, even functions and other components.

```jsx
<UserCard name="Ada" age={36} skills={["math", "code"]} isAdmin />
```

- Strings can use quotes: `name="Ada"`.
- Everything else needs braces: `age={36}`.
- A prop with no value, like `isAdmin`, means `true`.

React passes all props to the component as **one object**:

```jsx
function UserCard(props) {
  return (
    <p>
      {props.name} is {props.age}
    </p>
  );
}
```

### Destructuring

Almost everyone destructures props in the parameter list, which is shorter and shows which props the component uses:

```jsx
function UserCard({ name, age, skills = [] }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Skills: {skills.join(", ") || "none"}</p>
    </div>
  );
}
```

`skills = []` is a **default value**, used when the parent does not pass that prop (or passes `undefined`).

### Spreading props

If you already have an object, spread it in:

```jsx
const user = { name: "Ada", age: 36 };
<UserCard {...user} />
```

Use this sparingly, since it hides which props are being passed.

### Props are read-only

A component must **never modify its own props**:

```jsx
// Wrong
function Bad({ items }) {
  items.push("new");  // mutates the parent's data
}
```

If a component needs to change something over time, it uses **state** ([Lesson 4](./[4]-State-With-useState.md)). Parents pass new props down, and the child re-renders.

### Passing functions as props

Props can be callbacks, which is how a child tells a parent something happened:

```jsx
function Child({ onSelect }) {
  return <button onClick={() => onSelect("hello")}>Pick</button>;
}

function Parent() {
  return <Child onSelect={(value) => console.log(value)} />;
}
```

---

## 3.3 The children prop and composition

Anything you place **between** a component's opening and closing tags is passed as a special prop called `children`:

```jsx
function Card({ title, children }) {
  return (
    <section className="card">
      <h2>{title}</h2>
      <div className="card-body">{children}</div>
    </section>
  );
}

function App() {
  return (
    <Card title="Profile">
      <p>Ada Lovelace</p>
      <button>Follow</button>
    </Card>
  );
}
```

`Card` does not need to know what is inside it. It only provides the frame.

### Composition over inheritance

React favors **composition**: building complex UIs by combining simple components. You rarely, if ever, use class inheritance to share behavior.

Common composition techniques:

**1. Containment** (using `children`), as above.

**2. Specialization** (a specific component built from a generic one):

```jsx
function Button({ variant = "primary", children, ...rest }) {
  return (
    <button className={`btn btn-${variant}`} {...rest}>
      {children}
    </button>
  );
}

function DeleteButton(props) {
  return <Button variant="danger" {...props}>Delete</Button>;
}
```

`...rest` collects all other props so they can be forwarded to the underlying `<button>` (for example `onClick` or `disabled`).

**3. Slots** (passing JSX through named props):

```jsx
function Layout({ sidebar, content }) {
  return (
    <div className="layout">
      <aside>{sidebar}</aside>
      <main>{content}</main>
    </div>
  );
}

<Layout sidebar={<Menu />} content={<Article />} />
```

---

## 3.4 Typing props with TypeScript

TypeScript checks that you pass the right props. If you created your project with `--template react-ts`, files use the `.tsx` extension (TypeScript + JSX).

Describe props with an `interface` or `type`:

```tsx
interface UserCardProps {
  name: string;
  age: number;
  skills?: string[];          // optional
  onSelect: (name: string) => void;
}

function UserCard({ name, age, skills = [], onSelect }: UserCardProps) {
  return (
    <div onClick={() => onSelect(name)}>
      <h2>{name}</h2>
      <p>{age} years old</p>
      <p>{skills.join(", ")}</p>
    </div>
  );
}
```

Now TypeScript will warn you if you forget `name`, pass a string for `age`, or misspell a prop.

### Typing children

Use `ReactNode`, the type for anything React can render:

```tsx
import type { ReactNode } from "react";

interface CardProps {
  title: string;
  children: ReactNode;
}

function Card({ title, children }: CardProps) {
  return (
    <section>
      <h2>{title}</h2>
      {children}
    </section>
  );
}
```

### Extending native element props

To make a wrapper that accepts every normal `<button>` attribute, use `ComponentProps`:

```tsx
import type { ComponentProps } from "react";

type ButtonProps = ComponentProps<"button"> & {
  variant?: "primary" | "danger";
};

function Button({ variant = "primary", ...rest }: ButtonProps) {
  return <button className={`btn btn-${variant}`} {...rest} />;
}
```

The union `"primary" | "danger"` means TypeScript only accepts those two exact strings.

[Previous](./[2]-JSX-And-Elements.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[4]-State-With-useState.md)