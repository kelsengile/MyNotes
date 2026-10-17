[Previous](./[4]-Data-Binding-And-Directives.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[6]-Signals-And-Reactivity.md)

*Core Concepts*

# Lesson 5 - Component Communication

Real apps are trees of components that need to exchange data. This lesson covers the main tools: inputs and outputs for parent/child talk, content projection for flexible layouts, and references for reaching into children.

---

## 5.1 Inputs and outputs

**Inputs** pass data **down** from parent to child. **Outputs** send events **up** from child to parent.

```ts
import { Component, input, output } from '@angular/core';

@Component({
  selector: 'app-task-item',
  template: `
    <label>
      <input type="checkbox" [checked]="done()" (change)="toggled.emit()" />
      {{ title() }}
    </label>
    <button (click)="removed.emit(id())">Delete</button>
  `,
})
export class TaskItemComponent {
  id = input.required<number>();       // parent MUST provide it
  title = input.required<string>();
  done = input(false);                 // optional, default false

  toggled = output<void>();            // event with no payload
  removed = output<number>();          // event with a number payload
}
```

The parent binds inputs with `[ ]` and listens to outputs with `( )`:

```ts
@Component({
  selector: 'app-task-list',
  imports: [TaskItemComponent],
  template: `
    @for (t of tasks; track t.id) {
      <app-task-item
        [id]="t.id"
        [title]="t.title"
        [done]="t.done"
        (toggled)="toggle(t)"
        (removed)="remove($event)"
      />
    }
  `,
})
export class TaskListComponent {
  tasks = [{ id: 1, title: 'Learn Angular', done: false }];

  toggle(t: { done: boolean }): void { t.done = !t.done; }
  remove(id: number): void { this.tasks = this.tasks.filter((t) => t.id !== id); }
}
```

Points to remember:

- An input is a **signal**, so you read it by calling it: `title()`.
- Inside the output's listener, `$event` is whatever you passed to `emit()`.
- Data flows **down**, events flow **up**. A child should not modify the object a parent gave it; it should emit an event and let the parent decide.
- Input options: `input<number>(0, { alias: 'count', transform: numberAttribute })`. A *transform* converts raw template strings, e.g. `booleanAttribute` lets you write `<app-x disabled />`.

> **Older syntax:** `@Input() title = ''` and `@Output() removed = new EventEmitter<number>()`. They still work and you will see them often. `input()` and `output()` are the modern replacements, and they work with signals.

---

## 5.2 Content projection

Inputs pass data. **Content projection** passes *markup*: the parent places HTML between a component's tags, and the component decides where it appears using `<ng-content>`.

```ts
@Component({
  selector: 'app-card',
  template: `
    <div class="card">
      <header><ng-content select="[card-title]" /></header>
      <section><ng-content /></section>
      <footer><ng-content select="[card-actions]" /></footer>
    </div>
  `,
})
export class CardComponent {}
```

```html
<app-card>
  <h3 card-title>Order #42</h3>
  <p>Two items, shipped Tuesday.</p>
  <div card-actions><button>Track</button></div>
</app-card>
```

- `<ng-content />` with no `select` receives everything not claimed by another slot (the **default slot**).
- `select` takes a CSS selector: an attribute (`[card-title]`), an element (`h3`), or a class (`.actions`).
- Projected content is created and owned by the **parent**, so it can use the parent's data and styles.

For content the child needs to *render with its own data*, pass a template and let the child place it with `ngTemplateOutlet`:

```ts
import { Component, TemplateRef, input } from '@angular/core';
import { NgTemplateOutlet } from '@angular/common';

@Component({
  selector: 'app-list',
  imports: [NgTemplateOutlet],
  template: `
    @for (item of items(); track item) {
      <ng-container
        [ngTemplateOutlet]="rowTemplate()"
        [ngTemplateOutletContext]="{ $implicit: item }"
      />
    }
  `,
})
export class ListComponent {
  items = input.required<string[]>();
  rowTemplate = input.required<TemplateRef<{ $implicit: string }>>();
}
```

The parent supplies `<ng-template #row let-item>...{{ item }}...</ng-template>`. Use `ng-content` first; reach for template outlets only when the child must feed data back into the projected markup.

---

## 5.3 Template references and ViewChild

A **template reference variable** (`#name`) points to an element or component *within the same template*:

```html
<input #email type="email" />
<button (click)="email.focus()">Focus the input</button>
```

To reach that element from the **class**, use a **view query**. The signal-based form is `viewChild()`:

```ts
import { Component, viewChild, ElementRef, afterNextRender } from '@angular/core';

@Component({
  selector: 'app-login',
  template: `<input #email type="email" /><app-task-list />`,
  imports: [TaskListComponent],
})
export class LoginComponent {
  emailInput = viewChild.required<ElementRef<HTMLInputElement>>('email');
  list = viewChild(TaskListComponent);          // may be undefined

  constructor() {
    afterNextRender(() => this.emailInput().nativeElement.focus());
  }
}
```

- `viewChild('email')` finds by template reference name; `viewChild(SomeComponent)` finds by class.
- `viewChild.required()` throws if the element is absent; plain `viewChild()` returns `undefined` when absent.
- Results are signals: call them like `this.emailInput()`.
- Query results are **not available in the constructor body**, because the view does not exist yet. Use `afterNextRender` or `effect` to react once they exist.

Related queries:

| Function | Finds |
|---|---|
| `viewChild` / `viewChildren` | Elements in the component's own template |
| `contentChild` / `contentChildren` | Projected elements (inside `<ng-content>`) |

> **Older syntax:** `@ViewChild('email') emailInput!: ElementRef;` with access inside `ngAfterViewInit`.

---

## 5.4 Parent and child patterns

Pick the simplest tool for how far apart two components are:

| Situation | Best tool |
|---|---|
| Parent gives data to a direct child | Input |
| Child notifies a direct parent | Output |
| Parent needs to call a child method | `viewChild()` (use sparingly) |
| Siblings, or components far apart | A shared **service** (Lesson 7) |
| Child edits a value the parent owns | `model()` for two-way binding (Lesson 6) |

**Smart and presentational components.** A good rule is to split components into two roles:

- **Presentational** (dumb) components receive data through inputs and report user actions through outputs. They know nothing about HTTP or services, so they are easy to test and reuse.
- **Container** (smart) components inject services, load data, and pass it to presentational children.

```html
<!-- container template -->
<app-task-item
  [title]="task.title"
  (removed)="store.remove($event)"
/>
```

**Avoid reaching deep.** If you pass an input through three layers of components only so a grandchild can use it, that is called *prop drilling*. Move the data into a service injected where it is needed.

**One-way flow keeps bugs findable.** When something looks wrong on screen, you can trace upward: which input fed this value, and which parent owns it? Two-way bindings and child-side mutation make that trace harder, so use them deliberately.

---

**Further reading:** [Components guide](https://angular.dev/guide/components)

[Previous](./[4]-Data-Binding-And-Directives.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[6]-Signals-And-Reactivity.md)
