[Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[2]-JSX-And-Elements.md)

*Basics*

# Lesson 1 - What Is React

## 1.1 The library vs framework question

React describes itself as a **library**, not a framework. The difference is about who is in control:

- A **library** is a set of tools *you call* when you need them. You decide how the app is structured.
- A **framework** is a structure *that calls your code*. It decides how routing, data loading, and folders work.

React only handles the **view layer**: turning data into user interface. It does not include:

- a router (moving between pages)
- a data-fetching solution
- a required folder layout
- a build tool

You pick those from the ecosystem. This gives you flexibility, but it also means you make more decisions. Frameworks built on top of React, such as **Next.js** and **React Router (framework mode)**, bundle those decisions for you. You will meet them in [Lesson 18](./[18]-Next-Steps-With-React.md).

> In this Topic we learn React itself first, because every React framework is built on the same ideas.

---

## 1.2 Declarative UI and the component model

### Imperative vs declarative

In **imperative** code you tell the browser *how* to change the page step by step:

```js
// Imperative: manual DOM updates
const button = document.querySelector("#like");
let likes = 0;
button.addEventListener("click", () => {
  likes += 1;
  button.textContent = `Likes: ${likes}`;
});
```

In **declarative** code you describe *what* the UI should look like for the current data, and React handles the updates:

```jsx
// Declarative: describe the UI for the current state
import { useState } from "react";

function LikeButton() {
  const [likes, setLikes] = useState(0);
  return <button onClick={() => setLikes(likes + 1)}>Likes: {likes}</button>;
}
```

Do not worry about `useState` yet. The key idea is that the button's text is *calculated from data* (`likes`), not changed by hand.

### Components

A **component** is a reusable piece of UI. Components are JavaScript functions that return markup. You combine small components into bigger ones, forming a **component tree**:

```text
App
├── Header
├── ProductList
│   └── ProductCard (repeated)
└── Footer
```

### How React keeps the screen in sync

When data changes, React calls your component functions again to get a new description of the UI. It compares the new description with the previous one (a process called **reconciliation**) and updates only the parts of the real DOM that changed. You write the "what", and React works out the "how".

### One-way data flow

Data in React flows **down** the tree, from parent to child, through *props* (covered in [Lesson 3](./[3]-Components-And-Props.md)). This makes it easier to trace where a value came from when debugging.

---

## 1.3 Setting up a project with Vite

**Vite** is a fast build tool and development server. It is the common way to start a plain React single-page app.

**Requirements:** a current LTS version of [Node.js](https://nodejs.org) (check with `node --version`) and a terminal.

Create a project:

```bash
npm create vite@latest my-app -- --template react
```

Use `--template react-ts` instead if you want TypeScript. Then:

```bash
cd my-app
npm install
npm run dev
```

The terminal prints a local address, usually `http://localhost:5173`. Open it in the browser to see the starter app. Vite's dev server uses **hot module replacement (HMR)**: when you save a file, the page updates instantly without a full reload.

The available npm scripts in `package.json`:

| Command | What it does |
|---|---|
| `npm run dev` | Starts the development server |
| `npm run build` | Creates an optimized production build in `dist/` |
| `npm run preview` | Serves the production build locally to test it |
| `npm run lint` | Runs ESLint on the project |

---

## 1.4 Tour of a React project

A fresh Vite React project looks like this:

```text
my-app/
├── public/            # Static files served as-is (favicon, etc.)
├── src/
│   ├── assets/        # Images and other imported files
│   ├── App.jsx        # The root component of your app
│   ├── App.css
│   ├── index.css      # Global styles
│   └── main.jsx       # Entry point
├── index.html         # The single HTML page
├── package.json       # Dependencies and scripts
├── vite.config.js     # Vite configuration
└── eslint.config.js   # Linting rules
```

### index.html

This is the only HTML page. It contains an empty container and loads your code:

```html
<body>
  <div id="root"></div>
  <script type="module" src="/src/main.jsx"></script>
</body>
```

This is why React apps built this way are called **single-page applications**: one HTML page whose content is filled in by JavaScript.

### src/main.jsx

The entry point connects React to the `root` element:

```jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";
import App from "./App.jsx";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

- `createRoot` tells React which DOM element it controls.
- `.render(...)` draws the component tree into it.
- `StrictMode` is a development-only helper. It runs extra checks (such as running some code twice) to help you find bugs. It has no effect in production.

### src/App.jsx

`App` is the top-level component. Everything you build is placed inside it. Try replacing its contents with:

```jsx
function App() {
  return <h1>Hello, React!</h1>;
}

export default App;
```

Save the file and the browser updates immediately. You have now written your first component.

[Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[2]-JSX-And-Elements.md)