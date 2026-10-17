[Previous](./[12]-HTTP-And-Interceptors.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[14]-State-Management.md)

*Building Applications*

# Lesson 13 - RxJS And Observables

RxJS is a library for working with **streams of values over time**. Angular's HTTP client, router events, and form `valueChanges` all use it. This lesson builds the vocabulary and the habits you need.

---

## 13.1 Observables explained

An **Observable** describes a stream of zero or more values that may arrive now or later, and may end with a completion or an error. Nothing happens until someone **subscribes**.

```ts
import { Observable } from 'rxjs';

const numbers$ = new Observable<number>((subscriber) => {
  subscriber.next(1);
  subscriber.next(2);
  subscriber.next(3);
  subscriber.complete();
});

numbers$.subscribe({
  next: (n) => console.log('value', n),
  error: (e) => console.error(e),
  complete: () => console.log('done'),
});
```

By convention, variables holding Observables end with `$` (`users$`).

Compared with a Promise:

| | Promise | Observable |
|---|---|---|
| Values | Exactly one | Zero, one, or many |
| Starts | Immediately | When subscribed (lazy) |
| Cancel | Not possible | `unsubscribe()` |
| Operators | `then`/`catch` | Dozens (`map`, `switchMap`, ...) |

**Creating observables** rarely needs `new Observable`:

```ts
import { of, from, interval, fromEvent, timer } from 'rxjs';

of(1, 2, 3);                          // emits 1, 2, 3 then completes
from([10, 20]);                       // from an array or Promise
interval(1000);                       // 0, 1, 2, ... every second, never completes
timer(2000);                          // one emission after 2 seconds
fromEvent(document, 'click');         // every click
```

**Subscribing returns a `Subscription`** you can cancel:

```ts
const sub = interval(1000).subscribe(console.log);
sub.unsubscribe();   // stop the stream
```

An unfinished stream like `interval` keeps running and leaking until you unsubscribe. This is why section 13.4 matters.

---

## 13.2 Common operators

**Operators** are functions you chain inside `.pipe(...)` to transform a stream. Each returns a new Observable; the original is untouched.

```ts
import { map, filter, tap, take } from 'rxjs';

of(1, 2, 3, 4, 5).pipe(
  filter((n) => n % 2 === 1),    // 1, 3, 5
  map((n) => n * 10),            // 10, 30, 50
  tap((n) => console.log(n)),    // side effect, value passes through
  take(2),                       // 10, 30 then complete
).subscribe();
```

Essential operators:

| Operator | What it does |
|---|---|
| `map` | Transform each value |
| `filter` | Keep values that pass a test |
| `tap` | Run a side effect (logging) |
| `take(n)`, `first()` | Take n values, then complete |
| `debounceTime(ms)` | Wait until emissions pause for `ms` |
| `distinctUntilChanged()` | Skip repeats of the same value |
| `catchError` | Recover from errors |
| `shareReplay(1)` | Share one subscription; replay the latest to late subscribers |

**Higher-order mapping** starts an inner Observable (like an HTTP call) for each outer value. The four versions differ in how they handle overlap:

| Operator | When a new value arrives while an inner stream is running... | Typical use |
|---|---|---|
| `switchMap` | **Cancel** the old one, start the new one | Search-as-you-type, route params |
| `concatMap` | **Queue** it; run one after another | Ordered saves |
| `mergeMap` | Run **in parallel** | Independent requests |
| `exhaustMap` | **Ignore** new ones until finished | Submit buttons (prevent double click) |

Search example:

```ts
results$ = this.searchControl.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap((q) => this.api.search(q ?? '')),
);
```

**Combining streams:**

```ts
import { combineLatest, forkJoin, merge } from 'rxjs';

combineLatest([user$, settings$]);   // emits [user, settings] whenever either changes
forkJoin([api.a(), api.b()]);        // waits for all to complete, then one array (like Promise.all)
merge(clicks$, keys$);               // interleave events from both
```

---

## 13.3 Subjects

A **Subject** is an Observable you can also push values into by calling `next()`. It lets non-RxJS code, such as a button click handler or a service method, feed a stream.

```ts
import { Subject, BehaviorSubject, ReplaySubject } from 'rxjs';

const messages$ = new Subject<string>();
messages$.subscribe((m) => console.log('A got', m));
messages$.next('hello');      // A got hello
```

| Type | Behavior | Use it for |
|---|---|---|
| `Subject` | Late subscribers miss earlier values | Events (notifications, clicks) |
| `BehaviorSubject(initial)` | Always holds a **current value**; new subscribers get it immediately | State |
| `ReplaySubject(n)` | Replays the last n values to new subscribers | Recent history |

A typical service pattern: keep the Subject private and expose a read-only Observable.

```ts
@Injectable({ providedIn: 'root' })
export class CartService {
  private readonly _items = new BehaviorSubject<CartItem[]>([]);
  readonly items$ = this._items.asObservable();       // consumers cannot call next()

  add(item: CartItem): void {
    this._items.next([...this._items.value, item]);   // .value reads the current state
  }
}
```

In new code, a **signal** often replaces a `BehaviorSubject` for plain state (Lesson 6), because it needs no subscription. Keep Subjects for event streams, or when you want RxJS operators on the state.

Never expose `next()` publicly. If any component can push into a stream, finding who changed state becomes a hunt.

---

## 13.4 The async pipe and unsubscribing

Every long-lived subscription must end, or it keeps running after the component is gone. Four options, from simplest:

**1. The `async` pipe** subscribes in the template, updates the view with each value, and unsubscribes on destroy.

```ts
@Component({
  selector: 'app-tasks',
  imports: [AsyncPipe],
  template: `
    @if (tasks$ | async; as tasks) {
      @for (t of tasks; track t.id) { <li>{{ t.title }}</li> }
    } @else {
      <p>Loading...</p>
    }
  `,
})
export class TasksComponent {
  tasks$ = inject(TaskApi).list();
}
```

Use **one** `async` per Observable: each `| async` creates its own subscription, which can mean duplicate HTTP calls. The `@if (x$ | async; as x)` pattern reads it once.

**2. `toSignal()`** turns the Observable into a signal and unsubscribes automatically:

```ts
tasks = toSignal(inject(TaskApi).list(), { initialValue: [] });
```

**3. `takeUntilDestroyed()`** for manual subscriptions:

```ts
constructor() {
  interval(1000).pipe(takeUntilDestroyed()).subscribe((n) => this.tick(n));
}
```

**4. Manual `unsubscribe()`** in `ngOnDestroy`, as a last resort.

Which streams need this? Infinite or long-lived ones: `interval`, `fromEvent`, `valueChanges`, Subjects, WebSockets. Streams that complete by themselves (`HttpClient` calls, `of`, `take(1)`) need no cleanup.

Debugging tip: if a `console.log` inside `tap` keeps printing after you navigate away from a page, a subscription is leaking.

---

**Further reading:** [RxJS documentation](https://rxjs.dev/guide/overview)

[Previous](./[12]-HTTP-And-Interceptors.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[14]-State-Management.md)
