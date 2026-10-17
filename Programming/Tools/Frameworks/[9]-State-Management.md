[Previous](./[8]-Components-And-Rendering.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[10]-Routing-And-Navigation.md)

*Building The Frontend*

# Lesson 9 - State Management

A static page is easy: you write it once and it never changes. Real applications are alive. Items are added to carts, forms are filled in, menus open and close, and data arrives from servers. Keeping the screen in sync with all of that changing information is the problem **state management** solves. It is also where many beginners (and many large applications) run into trouble, so it is worth understanding clearly.

## 9.1 What Is State

**State** is data that can change over time and that affects what the user sees.

Examples of state:

- Whether a dropdown menu is open or closed.
- The text currently typed in a search box.
- The items in a shopping cart.
- Whether the user is logged in, and who they are.
- A list of messages loaded from a server.

### The core idea: the UI is a function of state

Modern frameworks are built on this idea:

```text
UI = function(state)
```

Instead of manually finding page elements and editing them, you **change the state**, and the framework redraws the interface to match. You describe *what the screen should look like for any given state*, and the framework takes care of *how to get there*.

Here is that idea in plain JavaScript with no framework:

```javascript
let state = { count: 0 };

function render(state) {
  console.log(`Count is ${state.count}`);
}

function setState(changes) {
  state = { ...state, ...changes }; // create a new state object
  render(state);                    // then redraw the UI from it
}

render(state);
setState({ count: state.count + 1 });
setState({ count: state.count + 1 });
```

Output:

```text
Count is 0
Count is 1
Count is 2
```

The program never edits the output directly. It only calls `setState`, and `render` redraws from the new state. Frameworks automate exactly this loop, and make it efficient.

### State vs derived data

Not every piece of data should be stored as state. If a value can be **calculated** from existing state, calculate it instead of storing it. For example, if you store a list of cart items, the *total price* and the *item count* can be computed from that list. Storing them separately creates the risk that they drift out of sync.

### Different kinds of state

| Kind | Description | Example |
|---|---|---|
| **UI state** | Temporary interface details | Is the menu open? Which tab is selected? |
| **Form state** | What the user is typing and whether it is valid | Fields of a signup form |
| **Application state** | Data the whole app cares about | The logged-in user, the shopping cart |
| **Server state** | A copy of data that lives on a server | A list of products fetched from an API |
| **URL state** | Information stored in the address | The current page or search filter |

Treating these differently, and not forcing everything into one big pile, is a key part of managing state well.

---

## 9.2 Local vs Global State

### Local state

**Local state** belongs to a single component and is used only by it. A toggle button remembering whether it is on, or an input remembering its current text, are typical examples. Local state is the simplest and safest kind, because nothing else can accidentally change it.

### Sharing state between components

Problems begin when two components need the *same* data. The standard solution is to **lift the state up**: move it to the nearest common parent, and pass it down to both children as props, with events for changes.

```text
            Parent  (owns "selectedTask")
           /      \
   TaskList        TaskDetails
 (shows list,     (shows the selected task)
  reports clicks)
```

### Prop drilling

As an app grows, data may need to travel through many layers to reach a deeply nested component. Passing it through several components that do not use it themselves is called **prop drilling**:

```text
App (has "user")
 └── Layout          ← passes "user" along, doesn't use it
      └── Sidebar    ← passes "user" along, doesn't use it
           └── Avatar   ← finally uses "user"
```

It makes code tedious and brittle, since adding a piece of data means editing every layer in between.

### Global state

**Global state** is state that is accessible from many places in the app without drilling through layers. It is suited to data that is genuinely needed everywhere, such as:

- The current user and their permissions.
- The selected theme (light or dark) or language.
- The shopping cart.
- Notifications.

Frameworks give you several ways to make state global: context or provider mechanisms (React Context, Flutter's Provider), injectable services (Angular, NestJS), and dedicated stores (Redux, Pinia, and others, covered in 9.4).

### A rule of thumb

> **Keep state as local as possible, and only move it up or make it global when several parts of the app truly need it.**

Global state is powerful, but it also means that any part of the app might change it, which makes bugs harder to trace. Start local; widen the scope only when forced to.

---

## 9.3 Reactivity and Data Flow

### What is reactivity?

**Reactivity** is the framework's ability to notice that state changed and automatically update whatever depends on it. Frameworks achieve this in different ways.

| Approach | How it detects changes | Used by |
|---|---|---|
| **Re-render on update** | You call a setter function; the framework re-runs the component and diffs the result | React (`setState`-style updates), Flutter (`setState`) |
| **Proxies / reactive objects** | The framework wraps your data so that reading and writing it is tracked | Vue.js |
| **Compiler-generated updates** | The compiler works out at build time which assignments affect which parts of the page | Svelte |
| **Signals** | Small observable values that notify exactly the things that read them | Angular, Vue.js (refs), Svelte, and others |
| **Observable streams** | Data is modeled as streams of values over time | Angular with RxJS, Flutter with streams |

### A small reactive system

To demystify proxy-based reactivity, here is a tiny version. Wrapping an object in a JavaScript `Proxy` lets us run code whenever a property is set:

```javascript
function reactive(target, onChange) {
  return new Proxy(target, {
    set(obj, key, value) {
      obj[key] = value;
      onChange(key, value); // notify whoever depends on this data
      return true;
    },
  });
}

const state = reactive({ count: 0 }, (key, value) => {
  console.log(`${key} changed to ${value}`);
});

state.count = 1;
state.count++;
```

Output:

```text
count changed to 1
count changed to 2
```

Real frameworks track *which* parts of the UI read each property and update only those, but the principle is the same: **writing to data triggers updates automatically.**

### Direction of data flow

How data moves through an application affects how easy it is to understand.

**One-way (unidirectional) data flow.** Data moves in one direction: state flows down into the UI, and user actions flow back up as events that change the state.

```text
   State ──► UI ──► (user clicks) ──► Event ──► State updates ──► UI redraws
```

This is the default in React, Flutter, and most modern designs. It makes it easy to answer "why did this change?", because there is only one path.

**Two-way binding.** The UI and the data are linked so that a change on either side updates the other. A text input that automatically updates a variable as you type, and vice versa, is the classic case. Angular and Vue.js offer this as a convenience for forms (for example, `v-model` in Vue.js), and Svelte supports it through its `bind:` directive. It reduces boilerplate, but if overused it can make it harder to trace where a change started.

### Immutability

Many frameworks, especially those that rely on detecting changes by comparison, prefer you to **replace** data rather than **modify it in place**. Notice that in the `state_demo.js` example we created a *new* state object with `{ ...state, ...changes }` instead of editing the old one. Producing new values makes it easy for the framework to see that something changed, and avoids a whole category of surprising bugs. Check your framework's guidance: reactive-proxy frameworks like Vue.js allow direct modification, while React expects replacement.

---

## 9.4 State Management Approaches

There is no single best way to manage state. The right tool depends on *what kind of state* you have and how widely it is shared. Most applications use several approaches together.

### Built-in tools

Every framework includes a way to hold local state in a component: hooks such as `useState` in React, reactive refs in Vue.js, assignments in Svelte, signals and component properties in Angular, and `setState` in a Flutter `StatefulWidget`. For many small apps, these are all you need.

### Sharing through the component tree

Frameworks provide a way to share values down a subtree without passing props through every layer: **Context** in React, **provide/inject** in Vue.js, **Provider** in Flutter, and **dependency-injected services** in Angular.

### Dedicated stores

For larger apps, a **store** is a central place that holds shared state and defines the *only* ways it may be changed. Popular examples are listed below.

| Ecosystem | Examples of state-management libraries |
|---|---|
| React | Redux (with Redux Toolkit), Zustand |
| Vue.js | Pinia |
| Angular | NgRx, or simple signal-based services |
| Svelte | Svelte stores |
| Flutter | Provider, Riverpod, Bloc |

Stores give you a predictable structure, easier debugging, and often special developer tools that let you inspect every change. The cost is extra concepts and boilerplate, so adopt one when the app's complexity justifies it.

### Server state libraries

Data fetched from a server has special problems: loading and error states, caching, refreshing, and avoiding duplicate requests. Libraries such as **TanStack Query** and **SWR** treat this *server state* separately from your own client state and handle those problems for you.

### The URL as state

Information such as the current page, a search term, or a selected filter often belongs in the **URL**. That way, users can bookmark it, share it, and use the back button, and the app restores it on reload (Lesson 10).

### Choosing an approach

| Situation | Reasonable choice |
|---|---|
| A value used by one component | Local state |
| A value shared by two or three nearby components | Lift state up to a common parent |
| Data needed deep in the tree (theme, user) | Context / provider / injected service |
| Complex app-wide state with many interactions | A dedicated store |
| Data coming from an API | A server-state library or the framework's data-fetching tools |
| A filter or page the user may want to bookmark | The URL |

The most common mistake is reaching for the heaviest tool too early. Begin with local state, and add structure only when the app genuinely needs it.

---

[Previous](./[8]-Components-And-Rendering.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[10]-Routing-And-Navigation.md)
