[Previous](./[8]-Standalone-Components-And-Modules.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[10]-Routing-And-Navigation.md)

*Core Concepts*

# Lesson 9 - Lifecycle Hooks

Angular creates, updates, and destroys components for you. **Lifecycle hooks** are methods you add to run code at specific moments in that process. This lesson explains the order, the hooks you will use daily, and how to clean up properly.

---

## 9.1 ngOnInit and ngOnDestroy

The two most common hooks:

```ts
import { Component, OnInit, OnDestroy, inject } from '@angular/core';

@Component({
  selector: 'app-clock',
  template: `<p>{{ now }}</p>`,
})
export class ClockComponent implements OnInit, OnDestroy {
  now = new Date().toLocaleTimeString();
  private timerId?: number;

  ngOnInit(): void {
    // runs once, after inputs are set
    this.timerId = window.setInterval(() => {
      this.now = new Date().toLocaleTimeString();
    }, 1000);
  }

  ngOnDestroy(): void {
    // runs once, just before the component is removed
    clearInterval(this.timerId);
  }
}
```

**`constructor` vs `ngOnInit`.** Use the constructor only for simple setup such as `inject()` calls and wiring up effects. Put initialization that depends on **inputs** or that starts work (HTTP requests, timers) in `ngOnInit`, because in the constructor the inputs are not set yet.

Full order for one component (each runs only where it applies):

| Order | Hook | Runs |
|---|---|---|
| 1 | `constructor` | When the class is created |
| 2 | `ngOnChanges` | Before `ngOnInit` and whenever a decorator-based input changes |
| 3 | `ngOnInit` | Once, after the first `ngOnChanges` |
| 4 | `ngDoCheck` | Every change-detection run |
| 5 | `ngAfterContentInit` | Once, after projected content is initialized |
| 6 | `ngAfterContentChecked` | After every check of projected content |
| 7 | `ngAfterViewInit` | Once, after the component's view and children are initialized |
| 8 | `ngAfterViewChecked` | After every check of the view |
| 9 | `ngOnDestroy` | Once, just before destruction |

Implementing the interface (`implements OnInit`) is optional but catches typos in the method name.

---

## 9.2 ngOnChanges

`ngOnChanges` runs when a **decorator-based** `@Input()` changes. It receives a `SimpleChanges` object showing the previous and current values.

```ts
import { Component, Input, OnChanges, SimpleChanges } from '@angular/core';

@Component({
  selector: 'app-user-detail',
  template: `<p>{{ userId }}</p>`,
})
export class UserDetailComponent implements OnChanges {
  @Input() userId = 0;

  ngOnChanges(changes: SimpleChanges): void {
    if (changes['userId'] && !changes['userId'].firstChange) {
      console.log('from', changes['userId'].previousValue,
                  'to', changes['userId'].currentValue);
      this.loadUser();
    }
  }

  private loadUser(): void { /* fetch by this.userId */ }
}
```

`ngOnChanges` fires only for **input** changes triggered by the parent, not for changes you make inside the component.

**With signal inputs (Lesson 5/6), `ngOnChanges` is not needed.** Use `computed` for derived values and `effect` for side effects:

```ts
userId = input.required<number>();

constructor() {
  effect(() => this.loadUser(this.userId()));   // reruns when userId changes
}
```

When the work is async (a request per id), the RxJS approach with `toObservable(this.userId).pipe(switchMap(...))` also cancels stale requests. Keep `ngOnChanges` knowledge for reading older code.

---

## 9.3 View and content hooks

These hooks relate to child content:

- **View** = elements in the component's own template (and the child components it renders).
- **Content** = elements projected into the component through `<ng-content>` by its parent.

```ts
@Component({
  selector: 'app-chart',
  template: `<canvas #canvas></canvas>`,
})
export class ChartComponent implements AfterViewInit {
  @ViewChild('canvas') canvas!: ElementRef<HTMLCanvasElement>;

  ngAfterViewInit(): void {
    // the canvas exists now: safe to draw
    const ctx = this.canvas.nativeElement.getContext('2d');
    ctx?.fillRect(0, 0, 50, 50);
  }
}
```

| Hook | Use it for |
|---|---|
| `ngAfterContentInit` | Reading projected content (`@ContentChild`) after it is ready |
| `ngAfterViewInit` | Touching template elements, initializing third-party widgets |
| `ngDoCheck`, `ngAfter*Checked` | Rare; they run constantly, so keep them extremely light |

**The modern replacement for DOM-touching hooks** is `afterNextRender` (once) and `afterEveryRender` (every render). They run **only in the browser**, which makes them safe with server-side rendering where `window` and `document` do not exist:

```ts
import { afterNextRender } from '@angular/core';

constructor() {
  afterNextRender(() => {
    // browser-only: measure DOM, start a chart library, etc.
  });
}
```

Warning: changing a bound value inside a `Checked` hook can trigger an `ExpressionChangedAfterItHasBeenChecked` error, since Angular finds a value changed during its own check. Move such updates elsewhere or use signals.

---

## 9.4 Cleanup patterns

Anything that continues after a component is gone is a **leak**: it wastes memory, keeps running, and often causes bugs. Common sources are timers, event listeners, and subscriptions.

**Pattern 1 - `ngOnDestroy`:**

```ts
ngOnDestroy(): void {
  clearInterval(this.timerId);
  this.subscription?.unsubscribe();
}
```

**Pattern 2 - `DestroyRef`** lets you register cleanup right next to the thing it cleans up, even in a service or field initializer:

```ts
private destroyRef = inject(DestroyRef);

constructor() {
  const id = setInterval(() => this.tick(), 1000);
  this.destroyRef.onDestroy(() => clearInterval(id));
}
```

**Pattern 3 - `takeUntilDestroyed`** for Observables, the cleanest option:

```ts
import { takeUntilDestroyed } from '@angular/core/rxjs-interop';

constructor() {
  this.store.updates$
    .pipe(takeUntilDestroyed())          // in injection context: no argument needed
    .subscribe((u) => this.apply(u));
}

// outside the constructor, pass a DestroyRef:
start(): void {
  this.store.updates$
    .pipe(takeUntilDestroyed(this.destroyRef))
    .subscribe();
}
```

**Pattern 4 - let Angular do it:**

- `async` pipe and `toSignal()` unsubscribe automatically.
- `effect` callbacks use `onCleanup(...)` for per-run cleanup.
- Listeners added in templates, with `(click)`, or via `host`, are removed for you.

**What does *not* need cleanup:** Observables that complete on their own, such as `HttpClient` calls that emit once, and `ActivatedRoute` observables (the router manages them).

Checklist when you write `addEventListener`, `setInterval`, or `.subscribe(...)` yourself: where does it stop?

---

**Further reading:** [Component lifecycle](https://angular.dev/guide/components/lifecycle)

[Previous](./[8]-Standalone-Components-And-Modules.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[10]-Routing-And-Navigation.md)
