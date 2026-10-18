[Previous](./[1]-What-Is-React.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[3]-Components-And-Props.md)

*Basics*

# Lesson 2 - JSX And Elements

## 2.1 JSX syntax and rules

**JSX** is a syntax extension for JavaScript that lets you write HTML-like markup inside JS. It is not HTML: a build tool (Vite, via Babel or SWC) converts it into plain JavaScript function calls.

```jsx
const element = <h1 className="title">Hello</h1>;
```

JSX has a few rules that differ from HTML.

### Rule 1: Return a single root element

A component can return only one top-level element. Wrap siblings in a parent (or a Fragment, see 2.3):

```jsx
// Wrong: two roots
// return <h1>Title</h1><p>Text</p>;

// Right
return (
  <div>
    <h1>Title</h1>
    <p>Text</p>
  </div>
);
```

Wrapping multi-line JSX in parentheses after `return` avoids an automatic semicolon problem.

### Rule 2: Close every tag

Self-closing tags need a slash, even ones HTML lets you leave open:

```jsx
<img src="cat.png" alt="A cat" />
<input type="text" />
<br />
```

### Rule 3: Use camelCase attribute names

Because JSX becomes JavaScript, some HTML attributes are renamed:

| HTML | JSX |
|---|---|
| `class` | `className` |
| `for` | `htmlFor` |
| `tabindex` | `tabIndex` |
| `onclick` | `onClick` |
| `maxlength` | `maxLength` |

`aria-*` and `data-*` attributes keep their hyphenated form.

### Rule 4: Style is an object

The `style` attribute takes a JavaScript object, with camelCase property names:

```jsx
<p style={{ backgroundColor: "gold", fontSize: "20px" }}>Styled text</p>
```

The double braces are simply "braces for JavaScript" containing "an object literal".

---

## 2.2 Embedding expressions

Curly braces `{}` open a "window" back into JavaScript. You can put any **expression** (something that produces a value) inside them.

```jsx
function Greeting() {
  const name = "Ada";
  const year = new Date().getFullYear();

  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>Next year is {year + 1}.</p>
      <p>{name.toUpperCase()}</p>
    </div>
  );
}
```

You can use braces in two places:

1. **As content** between tags: `<h1>{title}</h1>`
2. **As attribute values**: `<img src={user.avatarUrl} alt={user.name} />`

You cannot use *statements* such as `if` or `for` directly inside braces. Use expressions (ternaries, `.map()`) instead, as shown in 2.4.

### Elements are objects

Each piece of JSX creates a lightweight JavaScript object called a **React element** that describes what to draw. Elements are cheap to create and are never edited after creation. React makes a new one whenever the UI needs to change.

---

## 2.3 Fragments and keys

### Fragments

When you need to return several siblings without adding an extra `<div>` to the DOM, use a **Fragment**. The short form is an empty tag:

```jsx
function Profile() {
  return (
    <>
      <h1>Ada Lovelace</h1>
      <p>Mathematician</p>
    </>
  );
}
```

If you need to give the Fragment a `key` (see below), use the long form:

```jsx
import { Fragment } from "react";

<Fragment key={item.id}>
  <dt>{item.term}</dt>
  <dd>{item.definition}</dd>
</Fragment>
```

### Keys

When you render a list, each item needs a `key` prop: a value that is **unique among siblings** and **stable** over time. React uses keys to match items between renders, so it can tell which were added, removed, or reordered.

```jsx
const todos = [
  { id: 1, text: "Learn JSX" },
  { id: 2, text: "Build an app" },
];

<ul>
  {todos.map((todo) => (
    <li key={todo.id}>{todo.text}</li>
  ))}
</ul>
```

Key guidelines:

- Use an ID from your data (database ID, slug).
- Avoid using the array index as a key if the list can be reordered, inserted into, or deleted from. This can cause wrong state to stick to the wrong item.
- Do not generate keys during render with `Math.random()`: they change every time and defeat the purpose.
- The key must be on the **outermost element returned inside `.map()`**.

Keys are used by React and are not passed to your component as a prop.

---

## 2.4 Conditional and list rendering

### Conditional rendering

Since `if` statements cannot go inside JSX, there are a few patterns.

**1. Early return with `if`** (before the JSX):

```jsx
function Status({ isLoggedIn }) {
  if (!isLoggedIn) {
    return <p>Please sign in.</p>;
  }
  return <p>Welcome back!</p>;
}
```

**2. Ternary operator** (either/or inside JSX):

```jsx
<p>{isLoggedIn ? "Welcome back!" : "Please sign in."}</p>
```

**3. Logical AND `&&`** (show something or nothing):

```jsx
{hasUnread && <span className="badge">New</span>}
```

**Pitfall with `&&`:** if the left side is the number `0`, React renders `0` on the screen, since `0` is a valid thing to render. Convert to a boolean first:

```jsx
{items.length > 0 && <ItemList items={items} />}
```

React does not render `true`, `false`, `null`, or `undefined`, which is why `&&` and ternaries work.

### List rendering

Use `.map()` to turn an array into elements, and `.filter()` first if you only want some:

```jsx
function UserList({ users }) {
  const activeUsers = users.filter((user) => user.active);

  if (activeUsers.length === 0) {
    return <p>No active users.</p>;
  }

  return (
    <ul>
      {activeUsers.map((user) => (
        <li key={user.id}>
          {user.name} {user.isAdmin ? "(admin)" : ""}
        </li>
      ))}
    </ul>
  );
}
```

This combines everything from this lesson: expressions in braces, a ternary, and a list with keys.

[Previous](./[1]-What-Is-React.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[3]-Components-And-Props.md)