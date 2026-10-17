[Previous](./[18]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Angular.md)

*Quality And Delivery*

# Lesson 19 - Next Steps With Angular

You now know the core of Angular. This final lesson maps where to go next: scaling to multiple apps, splitting UIs across teams, deeper RxJS, and staying current as Angular evolves.

---

## 19.1 Nx monorepos

A **monorepo** keeps several apps and libraries in one Git repository. **Nx** is a popular tool that adds speed and structure to Angular monorepos.

Why teams use it:

- **Shared libraries:** a design system, API client, and utilities can be used by many apps without publishing to npm.
- **Task caching:** if nothing relevant changed, Nx replays previous build or test results instead of running them again.
- **Affected commands:** run only what a change could break.
- **Dependency graph:** visualize and enforce which projects may import which.
- **Code generators** for consistent apps, libraries, and components.

```bash
npx create-nx-workspace@latest        # new workspace (choose Angular)
nx serve my-app
nx test my-lib
nx affected -t test build             # only projects touched by your changes
nx graph                              # open the dependency graph
```

Typical layout:

```
apps/
  shop/                 # deployable application
  admin/
libs/
  shared/ui/            # buttons, cards
  shop/feature-cart/    # a feature library
  shared/data-access/   # API services
```

An Nx rule can stop `shared/ui` from importing a feature library, keeping dependencies one-directional. You can also add Nx to an existing Angular project with `nx init`.

Start with a single Angular CLI project. Move to a monorepo when you have **multiple apps sharing code** or many teams working in one codebase. Documentation: [nx.dev](https://nx.dev).

---

## 19.2 Micro-frontends

A **micro-frontend** architecture splits a large UI into independently built and deployed pieces, often owned by different teams, that load together into one "shell" application.

```
Shell app (navigation, auth)
  ├── loads "Orders" app   (Team A, own repo, own deploy)
  ├── loads "Billing" app  (Team B)
  └── loads "Profile" app  (Team C)
```

In Angular this is typically done with **Module Federation** (a webpack/esbuild feature) or the bundler-independent **Native Federation**, supported by libraries such as `@angular-architects/native-federation`. A **remote** app *exposes* components or routes, and the **host** loads them at runtime:

```ts
// host route (conceptual example)
{
  path: 'orders',
  loadChildren: () => loadRemoteModule('orders', './Routes').then((m) => m.ORDER_ROUTES),
}
```

Challenges to plan for:

- **Shared dependencies:** two apps loading different Angular versions can break; federation configs share singleton packages.
- **Consistent look and feel:** needs a shared design system.
- **Communication:** keep remotes loosely coupled using URLs, events, or a small shared contract instead of internal imports.
- **Operational complexity:** more pipelines, versioning, and testing across boundaries.

Micro-frontends solve an **organizational** problem (many teams deploying independently), not a technical one. For one team, lazy-loaded feature routes (Lesson 10.4) in a single app are simpler and usually enough.

---

## 19.3 Advanced RxJS

Lesson 13 gave you the foundation. Next topics:

**Sharing work between subscribers.** Without sharing, each subscriber repeats the HTTP request:

```ts
config$ = this.http.get<Config>('/api/config').pipe(
  shareReplay({ bufferSize: 1, refCount: true }),   // one request, cached for all subscribers
);
```

**Custom operators** wrap repeated pipelines into one reusable function:

```ts
import { MonoTypeOperatorFunction, pipe } from 'rxjs';

export function searchBox<T>(): MonoTypeOperatorFunction<string> {
  return pipe(debounceTime(300), distinctUntilChanged());
}

results$ = this.control.valueChanges.pipe(searchBox(), switchMap((q) => this.api.search(q)));
```

**Error and loading state together** with `startWith` and `catchError`:

```ts
vm$ = this.api.list().pipe(
  map((data) => ({ status: 'ok' as const, data })),
  startWith({ status: 'loading' as const, data: [] as Task[] }),
  catchError(() => of({ status: 'error' as const, data: [] as Task[] })),
);
```

**Other topics worth learning:**

| Topic | Why |
|---|---|
| `combineLatest`, `withLatestFrom`, `zip` | Combining several sources in the right way |
| `scan`, `reduce` | State machines and running totals in streams |
| `retry` / `repeat` with delays | Resilient polling and reconnecting (e.g. WebSockets with `webSocket`) |
| Schedulers (`asyncScheduler`) | Controlling when work runs |
| Marble testing (`TestScheduler`) | Testing time-based streams deterministically |
| `rxResource` and `resource` | Signal-based async data loading built on streams |

Practical advice: learn operators by **problem**, not by memorizing all of them. Browse the operator decision tree at [rxjs.dev](https://rxjs.dev) and ask, "what do I want to happen when a second value arrives?"

---

## 19.4 Keeping up with releases

Angular releases a **major version about every six months**, with minor versions and patches between. Each major version gets about **18 months** of support: 6 months of active support (bug fixes and features), then 12 months of long-term support (critical fixes and security patches only). Check [angular.dev/reference/releases](https://angular.dev/reference/releases) for the current schedule.

Angular promises predictable upgrades: breaking changes are announced and deprecated in advance, and the CLI automates most migrations.

**Upgrade workflow:**

```bash
ng version                          # see your current version
ng update                           # list available updates
ng update @angular/core @angular/cli   # perform the update
```

1. Visit the [Angular Update Guide](https://update.angular.dev) and choose your from/to versions; it lists the exact steps.
2. **Upgrade one major version at a time** (e.g. 19 to 20, then 20 to 21).
3. Commit before updating, so you can review the changes made by schematics.
4. Run build and tests after each step.
5. Update third-party libraries (Material, NgRx) in the same pass; their major versions track Angular's.

**Habits that keep you current:**

- Follow the official **Angular blog** and release notes (`blog.angular.dev`) and the GitHub changelog.
- Read the **deprecation warnings** in your console and terminal; fixing them early makes upgrades smooth.
- Use migrations as learning opportunities: `ng generate @angular/core:control-flow`, `:inject`, `:standalone`.
- Try new features in a small side project before adopting them in production, and note which APIs are labeled *experimental* or *developer preview* in the docs; those may change.
- Keep tests healthy. A solid suite is the best protection when upgrading.

Recent releases brought signals, built-in control flow, deferrable views, zoneless change detection, and a new default test setup, so some details in older tutorials will be outdated. When in doubt, trust [angular.dev](https://angular.dev) for the current recommendation.

Where to go from here: build a complete small app (a task manager with routing, forms, and an API), read other people's Angular code, and contribute to open source. Congratulations on completing the Angular topic!

---

**Further reading:** [Nx](https://nx.dev) · [Update guide](https://update.angular.dev) · [Releases](https://angular.dev/reference/releases)

[Previous](./[18]-Building-And-Deployment.md) | [Table of Contents](./[0]-Introduction-to-Angular.md)
