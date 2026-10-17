[Previous](./[2]-TypeScript-For-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[4]-Data-Binding-And-Directives.md)

*Basics*

# Lesson 3 - Components And Templates

Components are the building blocks of every Angular app. A component is a piece of UI made of a TypeScript class, an HTML template, and optional styles. This lesson shows how those three parts fit together.

---

## 3.1 Anatomy of a component

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-greeting',
  template: `
    <h2>Hello, {{ name }}!</h2>
    <button (click)="rename()">Change name</button>
  `,
  styles: `
    h2 { color: rebeccapurple; }
  `,
})
export class GreetingComponent {
  name = 'Ada';

  rename(): void {
    this.name = 'Grace';
  }
}
```

The `@Component` decorator takes a configuration object:

| Property | Purpose |
|---|---|
| `selector` | The custom HTML tag used to place the component, e.g. `<app-greeting />` |
| `template` / `templateUrl` | Inline HTML, or a path to an `.html` file |
| `styles` / `styleUrl` | Inline CSS, or a path to a `.css` file |
| `imports` | Other components, directives, and pipes the template uses |
| `changeDetection` | How Angular checks for updates (Lesson 16) |

Larger components usually use separate files:

```ts
@Component({
  selector: 'app-greeting',
  templateUrl: './greeting.component.html',
  styleUrl: './greeting.component.css',
})
```

To **use** a component inside another, import its class and list it in the parent's `imports`:

```ts
@Component({
  selector: 'app-root',
  imports: [GreetingComponent],
  template: `<app-greeting />`,
})
export class AppComponent {}
```

If you forget the `imports` entry, Angular reports an unknown-element error. Components are **standalone** by default in recent versions (Lesson 8). In Angular 18 and older, add `standalone: true` to the decorator.

Create a component with the CLI: `ng generate component greeting`.

---

## 3.2 Template syntax

A template is HTML plus a few Angular additions. It can read any public or protected member of the component class.

**Interpolation** prints a value:

```html
<p>{{ name }}</p>
<p>{{ count * 2 }}</p>
<p>{{ user.name.toUpperCase() }}</p>
```

Anything inside `{{ }}` is an *expression*. Expressions can read values, call methods, and do simple math or comparisons. They cannot contain statements such as `if`, `for`, or assignments with `=` (use events for those).

**Binding to properties and events** (covered in depth in Lesson 4):

```html
<img [src]="photoUrl" [alt]="name" />
<button (click)="save()">Save</button>
```

**Template reference variables** give an element a name you can use elsewhere in the same template:

```html
<input #search placeholder="Search" />
<button (click)="find(search.value)">Go</button>
```

**`@let`** declares a local variable inside a template, handy for avoiding repeated work:

```html
@let total = price * quantity;
<p>Total: {{ total }}</p>
```

Template expressions are written in a TypeScript-like language, but the template is *checked* at build time against your component's types. A typo such as `{{ nmae }}` fails the build instead of silently showing nothing.

---

## 3.3 Built-in control flow (@if, @for)

Control-flow blocks decide **what to render**. They use an `@` syntax and need no imports.

**`@if`** shows content conditionally:

```html
@if (user) {
  <p>Welcome, {{ user.name }}</p>
} @else if (loading) {
  <p>Loading...</p>
} @else {
  <p>Please log in.</p>
}
```

**`@for`** repeats content for each item. The `track` expression is **required**: it tells Angular how to identify each item so it can update the DOM efficiently.

```html
<ul>
  @for (task of tasks; track task.id) {
    <li>{{ $index + 1 }}. {{ task.title }}</li>
  } @empty {
    <li>No tasks yet.</li>
  }
</ul>
```

Inside `@for` you get helper variables:

| Variable | Meaning |
|---|---|
| `$index` | Current index (0-based) |
| `$count` | Number of items |
| `$first`, `$last` | `true` for the first / last item |
| `$even`, `$odd` | `true` for even / odd positions |

For simple lists of primitives with no id, use `track $index` or `track item`.

**`@switch`** picks one branch by value:

```html
@switch (status) {
  @case ('loading') { <p>Loading...</p> }
  @case ('error')   { <p>Something went wrong.</p> }
  @default          { <p>Done.</p> }
}
```

> **Older syntax:** before control flow blocks, templates used `*ngIf`, `*ngFor`, and `[ngSwitch]` (directives from `CommonModule`). They still work and appear in older projects, but new code should use `@if`, `@for`, and `@switch`. The CLI can convert old templates: `ng generate @angular/core:control-flow`.

---

## 3.4 Component styles and encapsulation

Styles you attach to a component apply **only to that component** by default. This is called **view encapsulation**. Angular achieves it by adding unique attributes to the component's elements and rewriting your selectors to match.

```css
/* greeting.component.css */
h2 { color: rebeccapurple; }   /* affects only <h2> inside GreetingComponent */
```

Two other useful selectors:

```css
:host {                 /* the component's own element, e.g. <app-greeting> */
  display: block;
  padding: 1rem;
}

:host(.highlight) {     /* when the host element has class "highlight" */
  background: #fff8c5;
}
```

You can change the encapsulation mode per component:

| Mode | Behavior |
|---|---|
| `ViewEncapsulation.Emulated` (default) | Scoped styles via generated attributes |
| `ViewEncapsulation.None` | Styles become global |
| `ViewEncapsulation.ShadowDom` | Uses the browser's native Shadow DOM |

```ts
import { Component, ViewEncapsulation } from '@angular/core';

@Component({
  selector: 'app-banner',
  template: `<p>Banner</p>`,
  styles: `p { font-weight: bold; }`,
  encapsulation: ViewEncapsulation.None,
})
export class BannerComponent {}
```

Where should shared styles go?

- **Global styles** (fonts, resets, CSS variables) belong in `src/styles.css`.
- **Component-specific styles** belong with the component.
- **Theme values** shared across components work well as CSS custom properties (`--brand-color`) declared in global styles and used inside components.

Avoid `::ng-deep` to reach into child components; it is deprecated. Prefer CSS custom properties or inputs so a parent can customize a child safely.

---

**Further reading:** [Components guide](https://angular.dev/guide/components) · [Templates guide](https://angular.dev/guide/templates)

[Previous](./[2]-TypeScript-For-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[4]-Data-Binding-And-Directives.md)
