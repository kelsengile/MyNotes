[Previous](./[15]-Testing-Angular-Applications.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[17]-UI-With-Angular-Material.md)

*Quality And Delivery*

# Lesson 16 - Performance And Change Detection

Angular is fast by default, but large apps can slow down. This lesson explains how Angular decides when to update the screen, and the tools that make updates cheaper and first loads faster.

---

## 16.1 How change detection works

**Change detection** is the process of checking your component data and updating the DOM to match. Angular walks the component tree and re-evaluates template bindings.

Two things must happen: Angular must be **notified** that something may have changed, and then it must **check** the views.

**Zone.js era (classic).** Angular patched browser APIs (clicks, timers, HTTP) using a library called **zone.js**. After any async task finished, Angular ran change detection on the whole tree. It worked automatically but was broad and added bundle weight.

**Signals and zoneless (modern).** With signals, Angular knows exactly which template reads which signal, so it can be notified precisely. **Zoneless** mode removes zone.js and schedules updates from signals and framework events:

- Recent Angular versions make zoneless the default for new projects. Older apps can opt in with `provideZonelessChangeDetection()` and remove zone.js from the build.
- In zoneless apps, changing a **plain field** inside a `setTimeout` will not refresh the view. Use **signals** for state that appears in the template, or call `ChangeDetectorRef.markForCheck()`.

```ts
// app.config.ts (opt in to zoneless in older projects)
providers: [provideZonelessChangeDetection()]
```

A few facts that explain performance:

- Template expressions run on **every** check, so keep them cheap. Avoid heavy method calls such as `{{ expensiveFilter() }}` that re-run constantly; use `computed()` (cached) or a pure pipe instead.
- `ExpressionChangedAfterItHasBeenChecked` means a value changed while Angular was verifying it (Lesson 9.3). Fix it by moving the change earlier or using signals.
- In development mode Angular runs a second verification pass; this is normal, and it is removed in production builds.

The Angular DevTools browser extension (Profiler tab) shows how often change detection runs and how long it takes.

---

## 16.2 OnPush strategy

`ChangeDetectionStrategy.OnPush` tells Angular: "Only check this component when it has a reason to be checked." The reasons are:

1. An **input** reference changed (or a signal input changed).
2. An **event** handled in the component (or a child) fired.
3. A **signal** read in its template changed.
4. An `async` pipe received a new value.
5. You called `markForCheck()` manually.

```ts
import { ChangeDetectionStrategy, Component, input } from '@angular/core';

@Component({
  selector: 'app-task-row',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `<li>{{ task().title }}</li>`,
})
export class TaskRowComponent {
  task = input.required<Task>();
}
```

This skips whole branches of the tree on most checks, which matters for big lists.

The main rule for OnPush: **do not mutate objects; replace them.**

```ts
// Breaks OnPush: same object reference, Angular sees no input change
this.task.title = 'New title';

// Works: a new object
this.task = { ...this.task, title: 'New title' };
```

Signals fit OnPush naturally, since changing a signal marks the component dirty automatically. A good habit: **use OnPush on every component** and keep state in signals or immutable inputs. Some newer Angular versions are moving toward OnPush as the default, so this habit stays valid either way.

Also helpful with large lists:

- Always give `@for` a good `track` expression (usually a stable `id`). It lets Angular reuse DOM nodes instead of recreating them.
- For thousands of rows, use virtual scrolling from the CDK (`cdk-virtual-scroll-viewport`).

---

## 16.3 Deferrable views

`@defer` lazy-loads **part of a template**. Everything inside it (and its dependencies, such as a heavy chart component) is split into a separate file and only downloaded when a trigger fires.

```html
@defer (on viewport) {
  <app-sales-chart [data]="sales()" />
} @placeholder {
  <div class="chart-skeleton">Chart will appear here</div>
} @loading (minimum 500ms) {
  <p>Loading chart...</p>
} @error {
  <p>Could not load the chart.</p>
}
```

The blocks:

| Block | Shown when |
|---|---|
| `@defer` | The content is loaded and the trigger fired |
| `@placeholder` | Before loading starts (also what `on viewport` watches) |
| `@loading` | While the file is downloading (`minimum` avoids flicker) |
| `@error` | If the download fails |

**Triggers** (`on ...`) say when to load:

| Trigger | Loads |
|---|---|
| `on idle` (default) | When the browser is idle |
| `on viewport` | When the placeholder scrolls into view |
| `on interaction` | On click or keypress of the placeholder |
| `on hover` | When the pointer hovers |
| `on immediate` | Right away, but after the initial render |
| `on timer(5s)` | After a delay |
| `when condition` | When an expression becomes true |

You can **prefetch** earlier than you show:

```html
@defer (on interaction; prefetch on idle) {
  <app-comments />
} @placeholder {
  <button>Show comments</button>
}
```

Conditions for `@defer`: the components inside must be **standalone** and must not be referenced elsewhere in the same file outside of deferred blocks (otherwise they stay in the main bundle). Use deferrable views for below-the-fold content, heavy widgets, and rarely opened panels. Do not defer content visible on first paint; it hurts the Largest Contentful Paint metric.

---

## 16.4 SSR and hydration

By default Angular renders in the browser: the user downloads JavaScript, then sees the page. **Server-side rendering (SSR)** renders the first HTML on a server, so users (and search crawlers) see content immediately, then the app takes over in the browser.

Add it with the CLI:

```bash
ng add @angular/ssr            # existing project
ng new my-app --ssr            # new project
```

**Hydration** is the step where the browser's Angular **reuses** the server-rendered DOM instead of throwing it away and re-rendering. It is enabled in `app.config.ts`:

```ts
import { provideClientHydration, withEventReplay } from '@angular/platform-browser';

providers: [provideClientHydration(withEventReplay())]
```

`withEventReplay()` records user clicks made before the app finished loading and replays them once ready.

Benefits: faster visible content, better SEO, better Core Web Vitals. Costs: you need a Node server (or prerendering), and your code runs in two places.

**Writing SSR-safe code.** On the server there is no `window`, `document`, or `localStorage`. Guard browser-only code:

```ts
import { afterNextRender, inject, PLATFORM_ID } from '@angular/core';
import { isPlatformBrowser } from '@angular/common';

constructor() {
  afterNextRender(() => {
    // browser only: safe to use window/document here
    localStorage.setItem('visited', 'true');
  });
}

private isBrowser = isPlatformBrowser(inject(PLATFORM_ID));
```

Hydration also requires the server and browser HTML to match. Manually changing the DOM (`innerHTML`, `document.createElement`) before hydration can cause mismatch errors; use Angular templates instead.

**Incremental hydration** goes further: combined with `@defer (hydrate on viewport)`, parts of the page stay as static HTML until needed.

---

**Further reading:** [Runtime performance](https://angular.dev/best-practices/runtime-performance)

[Previous](./[15]-Testing-Angular-Applications.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[17]-UI-With-Angular-Material.md)
