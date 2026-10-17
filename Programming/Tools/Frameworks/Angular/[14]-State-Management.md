[Previous](./[13]-RxJS-And-Observables.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[15]-Testing-Angular-Applications.md)

*Building Applications*

# Lesson 14 - State Management

**State** is the data your app remembers: the logged-in user, a shopping cart, the current filter. As apps grow, deciding where state lives and who may change it becomes the main design problem. This lesson walks from the simplest approach to dedicated libraries.

---

## 14.1 Service-based state

Often the best state manager is just a service, because a `providedIn: 'root'` service is a shared singleton (Lesson 7). With signals it becomes compact and readable:

```ts
@Injectable({ providedIn: 'root' })
export class CartStore {
  private readonly _items = signal<CartItem[]>([]);

  readonly items = this._items.asReadonly();                       // read-only to outsiders
  readonly count = computed(() => this._items().length);
  readonly total = computed(() =>
    this._items().reduce((sum, i) => sum + i.price * i.qty, 0),
  );

  add(item: CartItem): void {
    this._items.update((list) => [...list, item]);
  }

  remove(id: number): void {
    this._items.update((list) => list.filter((i) => i.id !== id));
  }

  clear(): void {
    this._items.set([]);
  }
}
```

Any component can inject `CartStore`, read `count()` or `total()`, and call `add()`. Principles to follow:

1. **Single source of truth.** One owner for each piece of data.
2. **Expose read-only state.** Components read signals but can only change state by calling methods.
3. **Derive, do not duplicate.** Use `computed` for totals, filters, and counts.
4. **Immutable updates.** Replace arrays and objects instead of mutating them.

The older RxJS version used a private `BehaviorSubject` and a public `items$` Observable (Lesson 13.3). It works, but needs `async` or `toSignal` in consumers.

Where this approach strains: many features changing the same state, a need to trace *why* state changed, undo/redo, or large teams wanting strict conventions. That is when libraries help.

---

## 14.2 NgRx basics

**NgRx** is the most widely used state library for Angular, inspired by Redux. It enforces a one-way cycle:

```
Component --dispatch--> Action --> Reducer --> Store (state) --select--> Component
                                      ^
                                   Effects (side effects like HTTP)
```

Install: `ng add @ngrx/store`. Core pieces:

**Actions** describe *what happened*:

```ts
import { createActionGroup, emptyProps, props } from '@ngrx/store';

export const CartActions = createActionGroup({
  source: 'Cart',
  events: {
    'Add Item': props<{ item: CartItem }>(),
    'Remove Item': props<{ id: number }>(),
    'Clear': emptyProps(),
  },
});
```

**Reducer** is a pure function: `(state, action) => newState`. It never mutates.

```ts
import { createFeature, createReducer, on } from '@ngrx/store';

interface CartState { items: CartItem[]; }
const initialState: CartState = { items: [] };

export const cartFeature = createFeature({
  name: 'cart',
  reducer: createReducer(
    initialState,
    on(CartActions.addItem, (state, { item }) => ({ ...state, items: [...state.items, item] })),
    on(CartActions.removeItem, (state, { id }) => ({ ...state, items: state.items.filter((i) => i.id !== id) })),
    on(CartActions.clear, () => initialState),
  ),
});
```

`createFeature` also generates **selectors** such as `cartFeature.selectItems`.

**Register and use:**

```ts
// app.config.ts
providers: [provideStore({ [cartFeature.name]: cartFeature.reducer })]

// component
private store = inject(Store);
items = this.store.selectSignal(cartFeature.selectItems);

add(item: CartItem): void {
  this.store.dispatch(CartActions.addItem({ item }));
}
```

**Effects** (`@ngrx/effects`) handle side effects such as loading data: they listen for actions, call an API, then dispatch success or failure actions. The **Redux DevTools** browser extension lets you inspect every action and replay state, which is NgRx's biggest benefit.

The cost is boilerplate: for a tiny change you touch actions, reducer, and selectors.

---

## 14.3 Signal-based stores

**NgRx SignalStore** (`@ngrx/signals`) keeps NgRx's structure but with far less ceremony, built on signals.

```ts
import { signalStore, withState, withComputed, withMethods, patchState } from '@ngrx/signals';
import { computed } from '@angular/core';

interface TodoState { todos: Todo[]; filter: 'all' | 'done'; }

export const TodoStore = signalStore(
  { providedIn: 'root' },
  withState<TodoState>({ todos: [], filter: 'all' }),

  withComputed(({ todos, filter }) => ({
    visible: computed(() =>
      filter() === 'done' ? todos().filter((t) => t.done) : todos(),
    ),
  })),

  withMethods((store) => ({
    add(title: string): void {
      patchState(store, (s) => ({ todos: [...s.todos, { id: Date.now(), title, done: false }] }));
    },
    setFilter(filter: 'all' | 'done'): void {
      patchState(store, { filter });
    },
  })),
);
```

```ts
export class TodoPageComponent {
  store = inject(TodoStore);     // store.visible(), store.add('...')
}
```

How it maps to what you already know:

| SignalStore piece | Plain-service equivalent |
|---|---|
| `withState` | `signal({...})` fields |
| `withComputed` | `computed()` fields |
| `withMethods` | Methods that update state |
| `patchState` | `signal.update()` with a partial object |

It offers conventions (consistent shape, plugin features such as entity collections) while staying lightweight. It is a good middle ground between a hand-written service and full Redux-style NgRx.

---

## 14.4 Choosing an approach

Start simple and move up only when you feel real pain.

| Situation | Suggested approach |
|---|---|
| State used by **one component** | A `signal()` in that component |
| Parent and children share state | Inputs / outputs / `model()` |
| State shared across a few unrelated components | **Service with signals** |
| Growing app, several teams, want consistent structure | **NgRx SignalStore** |
| Complex workflows, strict traceability, time-travel debugging, large enterprise teams | **NgRx Store + Effects** |

Questions that help you decide:

1. **Who needs this state?** If only one place, keep it local. Global state is more expensive than it looks.
2. **How often does it change, and by whom?** Many writers need stricter rules.
3. **Do you need to know *why* it changed?** An action log answers that.
4. **How big and varied is the team?** Libraries impose a shared vocabulary.

Distinguish **server state** (data owned by a backend, cached on the client) from **UI state** (is the menu open?). Server data is often better handled by caching at the data-fetching layer (a service with `shareReplay`, or `httpResource`/`resource`) than by copying everything into a global store.

Common mistakes:

- Putting every variable in a global store.
- Mutating state objects directly.
- Copying the same data into several places so they drift apart.
- Adopting NgRx on day one for an app with three screens.

---

**Further reading:** [NgRx documentation](https://ngrx.io/docs) · [NgRx SignalStore](https://ngrx.io/guide/signals)

[Previous](./[13]-RxJS-And-Observables.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[15]-Testing-Angular-Applications.md)
