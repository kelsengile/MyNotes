[Previous](./[7]-Development-Workflow.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[9]-State-Management.md)

*Building The Frontend*

# Lesson 8 - Components And Rendering

Modern frontend and mobile frameworks are built around one central idea: **build the interface out of components**. This lesson explains what a component is, how data and content flow into components, the different ways frameworks let you describe a component's appearance, and how frameworks turn those descriptions into pixels on the screen.

## 8.1 What Is a Component

A **component** is a reusable, self-contained piece of a user interface. It bundles together:

- **Structure**: what it looks like (its markup or widgets),
- **Behavior**: how it reacts to the user and to data,
- **Style**: how it is visually presented (often).

A button, a search box, a product card, a navigation bar, and an entire page can all be components. Small components combine to make bigger ones, and the result is a **tree**:

```text
ProductPage
├── NavigationBar
├── ProductCard
│   ├── ProductImage
│   ├── PriceTag
│   └── AddToCartButton
└── Footer
```

### Why components?

| Without components | With components |
|---|---|
| Copy and paste the same HTML in many places | Define it once, reuse it everywhere |
| A change must be made in every copy | A change is made in one place |
| Code for one feature is scattered across many files | Code for one feature lives together |
| Hard to test pieces in isolation | Each component can be tested alone |

### The same idea under different names

Every major UI framework has components, even when it uses a different word:

| Tool | Name for a component | Typical file |
|---|---|---|
| React / React Native | Component (a function that returns UI) | `.jsx`, `.tsx` |
| Angular | Component (a class with a decorator) | `.ts` with a template |
| Vue.js | Single-file component | `.vue` |
| Svelte | Component | `.svelte` |
| Flutter | **Widget** | `.dart` |

Learning to "think in components", breaking a screen into pieces and deciding which piece owns which data, is the most valuable skill in frontend development, and it transfers between all of these tools.

---

## 8.2 Props and Composition

### Props: passing data in

A component becomes truly reusable when it can be *configured from outside*. The inputs a component receives from its parent are called **props** (short for "properties"), or *inputs* in Angular, or *constructor parameters* in Flutter.

Props flow **one way**, from parent down to child. A child should treat its props as read-only; if something needs to change, the owner of that data changes it.

Here is the same `Greeting` component, which takes a `name`, written in five different tools. Compare them: only the syntax differs.

**React**

```jsx
function Greeting({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Using it:
<Greeting name="Ada" />
```

**Vue.js** (single-file component)

```vue
<script setup>
defineProps({ name: String });
</script>

<template>
  <h1>Hello, {{ name }}!</h1>
</template>
```

**Svelte**

```svelte
<script>
  let { name } = $props();
</script>

<h1>Hello, {name}!</h1>
```

**Angular**

```typescript
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-greeting',
  standalone: true,
  template: `<h1>Hello, {{ name }}!</h1>`,
})
export class GreetingComponent {
  @Input() name = '';
}

// Using it in another template:  <app-greeting name="Ada" />
```

**Flutter** (Dart)

```dart
class Greeting extends StatelessWidget {
  const Greeting({super.key, required this.name});

  final String name;

  @override
  Widget build(BuildContext context) {
    return Text('Hello, $name!');
  }
}

// Using it:  Greeting(name: 'Ada')
```

> Framework syntax evolves between versions, so always check the current documentation for the exact form. The concept, data passed down as named inputs, stays the same.

### Events: passing information back up

If data flows down, how does a child tell its parent that something happened, such as a button click? Through **events** or **callbacks**. The parent passes down a function (or an event handler), and the child calls it. Data goes **down** as props; messages go **up** as events.

```text
        Parent  (owns the data)
       props ↓       ↑ events
        Child   (displays data, reports clicks)
```

### Composition: building bigger things from smaller ones

**Composition** means assembling components by placing them inside each other. A particularly useful form is letting a component accept *other content* to render inside it. This is called **children** in React, **slots** in Vue.js and Svelte, and **content projection** in Angular, and it is done through a `child` argument or property in Flutter.

For example, a `Card` component can provide a border and padding and let the caller decide what goes inside:

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

// Anything can go inside it:
<Card>
  <h2>Welcome</h2>
  <p>This content is passed in as children.</p>
</Card>
```

Frameworks strongly favor **composition over inheritance**: instead of creating a complicated hierarchy of component classes that extend each other, you combine simple components like building blocks.

---

## 8.3 Templates, JSX, and Widgets

Frameworks differ most visibly in *how you describe a component's appearance*. There are three main approaches.

### Approach 1: Templates

A **template** looks like HTML with special markers for dynamic data, loops, and conditions. The framework processes it into working UI. Angular, Vue.js, and Svelte use templates, and so do server-side frameworks, for example Django and Flask (Jinja), Laravel (Blade), and ASP.NET Core (Razor).

Here is a server-side template turning a list of names into HTML:

```python
from jinja2 import Template

template = Template("<ul>{% for name in names %}<li>{{ name }}</li>{% endfor %}</ul>")
print(template.render(names=["Ada", "Grace"]))
```

Output:

```text
<ul><li>Ada</li><li>Grace</li></ul>
```

Templates are readable to anyone who knows HTML and keep structure and logic visibly separate.

### Approach 2: JSX

**JSX** is a syntax extension that lets you write HTML-like markup *inside JavaScript* (or TypeScript). It is used by React and React Native, and it is also available in some other tools. JSX is not understood by browsers directly; a build step converts it to ordinary JavaScript.

```jsx
function TaskList({ tasks }) {
  return (
    <ul>
      {tasks.map((task) => (
        <li key={task.id}>{task.title}</li>
      ))}
    </ul>
  );
}
```

Because the markup lives in a programming language, you can use the full power of that language (loops, functions, conditions) to build the interface. The `key` attribute helps the framework tell list items apart when the list changes.

### Approach 3: Widget trees in code

In **Flutter**, there is no markup language at all. The interface is described by **nesting widget objects in Dart code**:

```dart
Column(
  children: [
    Text('My Tasks'),
    ElevatedButton(
      onPressed: () { /* do something */ },
      child: Text('Add task'),
    ),
  ],
)
```

Layout itself is done with widgets, such as `Column`, `Row`, and `Padding`, rather than with a separate styling language.

### Comparing the approaches

| Approach | Used by | Strength | Consideration |
|---|---|---|---|
| **Templates** | Angular, Vue.js, Svelte, Django, Laravel, ASP.NET Core | Familiar HTML-like feel; clear separation of structure and logic | You learn template-specific syntax for loops and conditions |
| **JSX** | React, React Native | Full programming language available inside the markup | Mixing markup and logic can feel unusual at first |
| **Widget trees** | Flutter | One language for structure, layout, and behavior | Deeply nested code needs to be split into smaller widgets |

None of these is objectively best. They are different ways of expressing the same idea: *the interface is a function of the data it is given.*

---

## 8.4 Virtual DOM vs Compiled Rendering

When your data changes, the screen must change to match. How a framework does that efficiently is called its **rendering strategy**.

### The problem: touching the screen is expensive

In a browser, the page is represented by the **DOM** (Document Object Model), a tree of elements. Changing the DOM is comparatively slow, because the browser may need to recalculate layout and repaint. If a framework redrew the whole page on every small change, apps would feel sluggish. So frameworks try to update **only what changed**.

### Strategy 1: Virtual DOM

The **virtual DOM** is a lightweight copy of the page's structure kept in memory (plain JavaScript objects). When data changes:

1. The framework builds a **new** virtual tree from the new data.
2. It **compares** (diffs) the new tree to the previous one.
3. It applies **only the differences** to the real DOM.

```text
data changes → new virtual tree → diff against old tree → minimal real-DOM updates
```

React uses this approach, and so does Vue.js, which adds compile-time optimizations on top of it. The strength of the virtual DOM is that you write simple code ("here is the UI for this data") and the framework works out the smallest set of changes.

### Strategy 2: Compiled rendering

Instead of comparing trees while the app runs, some frameworks do the work **ahead of time, during the build**. They analyze your templates and generate code that knows exactly which part of the page depends on which piece of data, and updates just that part directly.

- **Svelte** is the best-known example: it compiles components into small, direct DOM-updating JavaScript and ships very little framework code to the browser.
- **Angular** also compiles templates ahead of time into instructions that update the DOM directly, and uses its own change-detection and signals mechanisms to know when to run them.

### Strategy 3: Drawing the interface yourself

**Flutter** does not use the DOM or native interface elements at all. It uses its own rendering engine to draw every pixel of every widget on a canvas. This is why Flutter apps look identical across platforms. **React Native** takes yet another path: it maps your components to the platform's *real native* interface elements.

### Comparing the strategies

| Strategy | Used by | Idea | Trade-off |
|---|---|---|---|
| **Virtual DOM** | React, Vue.js | Diff in-memory trees at runtime | A small runtime is shipped to the browser |
| **Compiled** | Svelte, Angular | Generate precise update code at build time | Requires a build step; less runtime work |
| **Own rendering engine** | Flutter | Draw everything on a canvas | Consistent look; app includes its own engine |
| **Native elements** | React Native | Map components to the platform's real UI | Uses native look, but depends on platform bridges |

### What this means for you

- All of these approaches can produce fast applications. For the great majority of projects, **rendering strategy is not the deciding factor**; team skills, ecosystem, and fit matter far more.
- It is still useful to know the difference, because it explains *why* each framework asks you to write code the way it does, and why certain performance advice differs between them (Lesson 18).

---

[Previous](./[7]-Development-Workflow.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[9]-State-Management.md)
