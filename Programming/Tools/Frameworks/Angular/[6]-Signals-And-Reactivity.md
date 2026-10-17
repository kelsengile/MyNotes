[Previous](./[5]-Component-Communication.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[7]-Services-And-Dependency-Injection.md)

*Core Concepts*

# Lesson 6 - Signals And Reactivity

Signals are Angular's modern way to track state that changes over time. When a signal changes, Angular knows exactly which parts of the UI depend on it and updates only those. This lesson also explains how signals relate to RxJS.

---

## 6.1 signal and computed

A **signal** is a wrapper around a value that notifies readers when it changes. You **read** it by calling it like a function.

```ts
import { Component, signal, computed } from '@angular/core';
import { CurrencyPipe } from '@angular/common';

@Component({
  selector: 'app-cart',
  imports: [CurrencyPipe],
  template: `
    <p>Items: {{ quantity() }}</p>
    <p>Total: {{ total() | currency }}</p>
    <button (click)="add()">Add</button>
    <button (click)="reset()">Reset</button>
  `,
})
export class CartComponent {
  quantity = signal(0);
  unitPrice = signal(9.99);

  total = computed(() => this.quantity() * this.unitPrice());

  add(): void {
    this.quantity.update((q) => q + 1);   // compute new value from the old
  }

  reset(): void {
    this.quantity.set(0);                  // replace the value
  }
}
```

- `signal(initial)` creates a **writable** signal. Change it with `.set(value)` or `.update(fn)`.
- `computed(() => ...)` creates a **read-only derived** signal. It re-calculates only when a signal it read changes, and it **caches** its result otherwise.
- Template reads such as `quantity()` are tracked automatically. When `quantity` changes, only the bindings that read it update.

Objects and arrays need *new* values so Angular sees a change:

```ts
tasks = signal<string[]>([]);

addTask(t: string): void {
  this.tasks.update((list) => [...list, t]);   // new array, not list.push(t)
}
```

Other useful pieces:

```ts
import { linkedSignal, untracked } from '@angular/core';

// writable signal that resets when its source changes
selected = linkedSignal(() => this.options()[0]);

// read a signal without registering a dependency
const snapshot = untracked(() => this.quantity());
```

Derive values with `computed()` instead of copying them into a second signal by hand; that keeps your state in one source of truth.

---

## 6.2 effect

An **effect** runs code in response to signal changes. It is for **side effects**: logging, saving to `localStorage`, syncing with a non-Angular library, drawing on a canvas.

```ts
import { effect } from '@angular/core';

export class ThemeComponent {
  theme = signal<'light' | 'dark'>('light');

  constructor() {
    effect(() => {
      document.documentElement.dataset['theme'] = this.theme();
    });
  }
}
```

How effects behave:

- An effect runs once at creation, then again whenever a signal it read changes.
- It must be created in an **injection context**, such as a constructor or a field initializer.
- It cleans itself up automatically when its component is destroyed.
- For cleanup between runs, use the callback argument:

```ts
effect((onCleanup) => {
  const id = setInterval(() => console.log(this.theme()), 1000);
  onCleanup(() => clearInterval(id));
});
```

**Do not use effects to compute state.** This is a common mistake:

```ts
// Avoid: copying one signal into another with an effect
effect(() => this.total.set(this.quantity() * this.unitPrice()));

// Prefer: derive it
total = computed(() => this.quantity() * this.unitPrice());
```

Rule of thumb: if you are producing a value, use `computed` or `linkedSignal`. If you are *doing something outside Angular's data model*, use `effect`.

---

## 6.3 Signal inputs and models

Lesson 5 introduced `input()`. Since inputs are signals, they plug straight into `computed` and `effect`:

```ts
@Component({
  selector: 'app-price-tag',
  template: `<span>{{ formatted() }}</span>`,
})
export class PriceTagComponent {
  amount = input.required<number>();
  currency = input('USD');

  formatted = computed(() =>
    new Intl.NumberFormat('en-US', { style: 'currency', currency: this.currency() })
      .format(this.amount())
  );
}
```

There is no `ngOnChanges` needed: when `amount` changes, `formatted` updates by itself.

A **model** is an input that the child can also *write to*, which enables two-way binding with `[( )]`:

```ts
import { model } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <button (click)="value.set(value() - 1)">-</button>
    {{ value() }}
    <button (click)="value.set(value() + 1)">+</button>
  `,
})
export class CounterComponent {
  value = model(0);
}
```

```html
<!-- parent -->
<app-counter [(value)]="count" />
<p>Parent sees: {{ count }}</p>
```

When the child calls `value.set(...)`, the parent's `count` updates too. If the parent's `count` is itself a signal, write `[(value)]="countSignal"`, with no call parentheses.

Interop helpers connect signals to RxJS (from `@angular/core/rxjs-interop`):

```ts
import { toSignal, toObservable } from '@angular/core/rxjs-interop';

const counter = toSignal(interval(1000), { initialValue: 0 });  // Observable -> Signal
const query$ = toObservable(this.query);                         // Signal -> Observable
```

`toSignal` subscribes immediately and unsubscribes automatically on destroy.

---

## 6.4 Signals vs RxJS

Angular uses two reactive tools. They solve different problems:

| | Signals | RxJS Observables |
|---|---|---|
| Best for | Current **state** and values derived from it | **Streams of events** over time |
| Always has a value? | Yes | No (may emit later, or never) |
| Reading | Synchronous: `count()` | Subscribe, or `async` pipe |
| Cleanup | Automatic for computed/effect | You manage subscriptions (or `async`/`takeUntilDestroyed`) |
| Strengths | Simple, fine-grained UI updates | Debounce, retry, cancel, combine, merge async work |

Practical guidance:

- **Use signals for component state** (`isOpen`, `selectedItem`, form model, filtered lists).
- **Use RxJS for async event flow** that needs time-based operators: typeahead search with `debounceTime`, canceling a request with `switchMap`, polling, WebSockets.
- **Bridge them** with `toSignal()` at the edge so the template only needs to read signals.

```ts
results = toSignal(
  toObservable(this.query).pipe(
    debounceTime(300),
    distinctUntilChanged(),
    switchMap((q) => this.api.search(q)),
  ),
  { initialValue: [] },
);
```

This keeps the stream logic in RxJS (where it shines) and the display in signals (where it is simplest). Angular's HTTP client still returns Observables, which you will use in Lessons 12 and 13.

---

**Further reading:** [Signals guide](https://angular.dev/guide/signals)

[Previous](./[5]-Component-Communication.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[7]-Services-And-Dependency-Injection.md)
