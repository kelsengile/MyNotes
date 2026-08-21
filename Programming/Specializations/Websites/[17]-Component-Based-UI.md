[Previous](./[16]-Testing-JavaScript.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[18]-React-Fundamentals.md)

*Front-End Frameworks*

# Lesson 17 - Introduction to Component-Based UI

## 17.1 The Core Idea

Modern front-end frameworks (React, Vue, Svelte, Angular) all share one foundational idea: breaking a UI into small, self-contained **components**, each owning its own markup, styling, and behavior, then composing those components together to build full pages. Instead of one long HTML file, a page becomes a tree of components: `App` → `Header`, `Sidebar`, `MainContent` → `Card`, `Card`, `Card`...

---

## 17.2 Why Components, Not Just Functions

A component is more than a function that returns HTML — it typically also manages its own **state** (data that changes over time and affects what's rendered) and reacts to **props** (data passed in from a parent). This combination lets a `<Button>` component, for example, be reused everywhere in an app while still customizing its label, color, and click behavior per usage.

---

## 17.3 Declarative vs Imperative UI

Before component frameworks, updating the DOM was largely **imperative**: you wrote step-by-step instructions ("find this element, change its class, append this node"). Component frameworks are **declarative**: you describe what the UI *should look like* for a given state, and the framework figures out the minimal DOM changes needed to get there. This shift is what makes complex, frequently-updating UIs manageable to write and reason about.

---

## 17.4 The Virtual DOM, Briefly

Many frameworks (React among them) use a **virtual DOM** — a lightweight in-memory representation of the UI. When state changes, the framework builds a new virtual DOM tree, compares ("diffs") it against the previous one, and applies only the minimal set of real DOM updates needed. This avoids the cost of naively re-rendering the entire page on every change. Not every framework uses this exact technique (Svelte, for instance, compiles away most of this work ahead of time), but the underlying goal — efficient, predictable updates — is shared across all of them.

---

## 17.5 Choosing a Framework

React, Vue, and Angular all solve the same core problem with different syntax and philosophy: React uses JSX (HTML-like syntax inside JavaScript) and a large surrounding ecosystem of choices; Vue offers a more batteries-included, template-based approach; Angular is a full, opinionated framework maintained by Google. This course focuses on React (Lesson 18) as the most widely adopted choice, but the concepts — components, props, state, declarative rendering — transfer directly to any of them.

---

[Previous](./[16]-Testing-JavaScript.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[18]-React-Fundamentals.md)
