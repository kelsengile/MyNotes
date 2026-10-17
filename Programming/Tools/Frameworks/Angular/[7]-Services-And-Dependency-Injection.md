[Previous](./[6]-Signals-And-Reactivity.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[8]-Standalone-Components-And-Modules.md)

*Core Concepts*

# Lesson 7 - Services And Dependency Injection

Components should focus on the screen. Logic that is shared, or that talks to the outside world, belongs in **services**. Angular delivers services to components through **dependency injection** (DI). This lesson explains both ideas.

---

## 7.1 What a service is

A **service** is a plain class that does a specific job: loading data, holding shared state, logging, authentication. It is marked with `@Injectable` so Angular can create and supply it.

```ts
import { Injectable, signal } from '@angular/core';

export interface Task { id: number; title: string; done: boolean; }

@Injectable({ providedIn: 'root' })
export class TaskService {
  private readonly _tasks = signal<Task[]>([]);
  readonly tasks = this._tasks.asReadonly();   // expose read-only view

  add(title: string): void {
    this._tasks.update((list) => [...list, { id: Date.now(), title, done: false }]);
  }
}
```

A component asks for the service with `inject()`:

```ts
import { Component, inject } from '@angular/core';

@Component({
  selector: 'app-task-page',
  template: `
    <button (click)="service.add('New task')">Add</button>
    <ul>
      @for (t of service.tasks(); track t.id) { <li>{{ t.title }}</li> }
    </ul>
  `,
})
export class TaskPageComponent {
  service = inject(TaskService);
}
```

Two components that inject `TaskService` receive the **same instance**, so they share its state. That is why services are the standard tool for sibling communication (Lesson 5.4).

**Dependency injection** means a class does not create its own dependencies with `new`. It *declares* what it needs and Angular supplies it. Benefits: shared instances, simple testing (swap in a fake), and loose coupling.

Create one with the CLI: `ng generate service task`.

---

## 7.2 The injector hierarchy

Angular keeps **injectors**, objects that know how to create and cache dependencies, arranged in a tree that mirrors your app. When you call `inject(Something)`, Angular asks the nearest injector first and walks upward until one can supply it.

```
Platform injector
  └─ Root environment injector      (providedIn: 'root', app config providers)
       └─ Route environment injectors   (route-level `providers`, lazy routes)
            └─ Element injectors          (component/directive `providers`)
```

Consequences:

- A service provided in the **root** injector is a **singleton** for the whole app.
- A service provided in a **component's** `providers` is created **per component instance**, and is shared with that component's children.
- If nothing in the chain provides the dependency, Angular throws `NullInjectorError`. The fix is almost always to add a `providedIn` or a provider.

```ts
@Component({
  selector: 'app-editor',
  template: `...`,
  providers: [EditorStateService],   // each <app-editor> gets its own instance
})
export class EditorComponent {
  state = inject(EditorStateService);
}
```

**Resolution modifiers** change how the lookup works:

```ts
const logger = inject(LoggerService, { optional: true });   // null instead of error
const own = inject(FormService, { self: true });             // only this element's injector
const parent = inject(MenuService, { skipSelf: true });      // start at the parent
const host = inject(HostService, { host: true });            // stop at the host element
```

Use `optional: true` when a dependency may legitimately be missing, and `skipSelf` for patterns like nested menus where a child talks to its parent's instance of the same service.

---

## 7.3 providedIn and providers

There are several places to tell Angular how to supply a dependency.

**1. `providedIn: 'root'`** (the default choice). The service is available app-wide, and bundlers can remove it if nothing uses it (*tree-shaking*).

```ts
@Injectable({ providedIn: 'root' })
export class AuthService {}
```

**2. The `providers` array** of a component, route, or `app.config.ts`:

```ts
export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    { provide: API_URL, useValue: 'https://api.example.com' },
  ],
};
```

A provider is an object with a **token** (`provide`) and a **recipe**:

| Recipe | Meaning | Example |
|---|---|---|
| `useClass` | Create an instance of this class | `{ provide: Logger, useClass: ConsoleLogger }` |
| `useValue` | Use this exact value | `{ provide: API_URL, useValue: '/api' }` |
| `useFactory` | Call this function to build it | `{ provide: Cache, useFactory: () => new Cache(100) }` |
| `useExisting` | Alias another token | `{ provide: Old, useExisting: New }` |

**Injection tokens** let you inject things that are not classes, such as strings, numbers, or config objects:

```ts
import { InjectionToken, inject } from '@angular/core';

export const API_URL = new InjectionToken<string>('API_URL');

// somewhere in a class:
private apiUrl = inject(API_URL);
```

A token can even provide its own default so no setup is needed:

```ts
export const PAGE_SIZE = new InjectionToken<number>('PAGE_SIZE', {
  providedIn: 'root',
  factory: () => 20,
});
```

Where to provide what:

- Cross-app singletons (auth, HTTP wrappers): `providedIn: 'root'`.
- Feature-only services: route `providers` so they exist only while that feature is loaded.
- Per-instance state (a form editor, a dialog): component `providers`.

---

## 7.4 inject() function

`inject()` retrieves a dependency from the current injector. It replaced **constructor injection** as the recommended style, though both work.

```ts
// Modern
export class OrderComponent {
  private http = inject(HttpClient);
  private route = inject(ActivatedRoute);
}

// Older: constructor parameter injection
export class OrderComponent {
  constructor(private http: HttpClient, private route: ActivatedRoute) {}
}
```

Why `inject()` is preferred:

- It works in **field initializers**, so no constructor is needed.
- It works in plain functions, such as functional route guards, interceptors, and resolvers (Lessons 10 and 12).
- It makes inheritance simpler, since subclasses do not have to pass dependencies to `super()`.
- Types are inferred, so there is less boilerplate.

**Injection context.** `inject()` only works while Angular is creating something: in a constructor, a field initializer, a provider factory, or a function Angular runs for you (guard, interceptor). Calling it later, such as inside a button click handler, throws an error:

```ts
// Wrong: runs after creation, outside the injection context
onClick(): void {
  const http = inject(HttpClient);   // NG0203 error
}

// Right: inject once, at creation
private http = inject(HttpClient);
onClick(): void {
  this.http.get('/api').subscribe();
}
```

If you really need to inject later, capture an `Injector` and use `runInInjectionContext`:

```ts
private injector = inject(Injector);

later(): void {
  runInInjectionContext(this.injector, () => {
    const router = inject(Router);
  });
}
```

This is a rare need. In most code, inject everything once as fields at the top of the class.

---

**Further reading:** [Dependency injection guide](https://angular.dev/guide/di)

[Previous](./[6]-Signals-And-Reactivity.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[8]-Standalone-Components-And-Modules.md)
