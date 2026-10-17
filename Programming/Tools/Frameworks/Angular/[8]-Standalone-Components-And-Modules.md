[Previous](./[7]-Services-And-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[9]-Lifecycle-Hooks.md)

*Core Concepts*

# Lesson 8 - Standalone Components And Modules

Angular used to require every component to be declared in an `NgModule`. Modern Angular uses **standalone** components, which manage their own dependencies. This lesson covers both worlds, because real projects often contain a mix.

---

## 8.1 Standalone components

A **standalone** component lists what its template needs directly in its own `imports`. No module is involved. Since Angular 19, components, directives, and pipes are standalone by default; before that you wrote `standalone: true`.

```ts
import { Component } from '@angular/core';
import { DatePipe } from '@angular/common';
import { RouterLink } from '@angular/router';
import { UserCardComponent } from './user-card.component';

@Component({
  selector: 'app-dashboard',
  imports: [UserCardComponent, DatePipe, RouterLink],
  template: `
    <app-user-card />
    <p>{{ today | date }}</p>
    <a routerLink="/settings">Settings</a>
  `,
})
export class DashboardComponent {
  today = new Date();
}
```

Rules of thumb:

- If the template uses a component, directive, or pipe, it must be in `imports`.
- The same rule applies to built-in things like `DatePipe`, `NgClass`, `RouterLink`, and `FormsModule`. (`@if` and `@for` need no import.)
- You may import **NgModules** into a standalone component too, e.g. `MatButtonModule`.

The app starts without a root module:

```ts
// main.ts
bootstrapApplication(AppComponent, {
  providers: [provideRouter(routes), provideHttpClient()],
});
```

Application-wide services and features are registered with `provide...()` functions (`provideRouter`, `provideHttpClient`, `provideAnimationsAsync`), which replace the old module imports (`RouterModule.forRoot`, `HttpClientModule`).

If a component's `imports` list is wrong, the CLI reports a clear error at build time, for example "`app-user-card` is not a known element."

---

## 8.2 NgModules and legacy code

An **NgModule** is a class decorated with `@NgModule` that groups related declarations and tells Angular how to compile them.

```ts
@NgModule({
  declarations: [ProductListComponent, ProductCardComponent, PricePipe],
  imports: [CommonModule, FormsModule, RouterModule],
  exports: [ProductListComponent],
  providers: [ProductService],
})
export class ProductsModule {}
```

| Property | Meaning |
|---|---|
| `declarations` | Components, directives, and pipes owned by this module |
| `imports` | Other modules whose exports this module's templates can use |
| `exports` | What this module makes available to modules that import it |
| `providers` | Services registered for DI |
| `bootstrap` | Root component (only in the root module) |

In an NgModule app, templates get their dependencies from the **module**, not the component. A component belongs to exactly one module through `declarations`, and that is the source of the classic "is not a known element" error when you forget to declare or export something.

The old bootstrap looks like this:

```ts
platformBrowserDynamic().bootstrapModule(AppModule);
```

**Mixing both worlds works:**

- A **standalone** component can import an NgModule (as with Angular Material modules).
- An **NgModule** can import a standalone component by listing it in `imports` (never in `declarations`).
- Use `importProvidersFrom(SomeModule)` to bring a module's providers into `bootstrapApplication`.

You will read NgModule code in older projects for years. New code should not add new modules unless a library requires one.

---

## 8.3 Organizing features

Without modules, folders and conventions provide the structure. A popular layout:

```
src/app/
├── core/                  # App-wide singletons: auth, interceptors, layout shell
│   ├── auth.service.ts
│   └── auth.interceptor.ts
├── shared/                # Reusable dumb UI: buttons, pipes, directives
│   ├── truncate.pipe.ts
│   └── card.component.ts
└── features/              # One folder per business area
    ├── tasks/
    │   ├── tasks.routes.ts
    │   ├── task-list.component.ts
    │   ├── task-item.component.ts
    │   └── task.service.ts
    └── profile/
        └── ...
```

Guidelines:

- **Organize by feature**, not by file type. A folder named `components/` containing 80 unrelated files is hard to navigate.
- **Core** is loaded once. **Shared** is imported where needed. **Features** are mostly self-contained and often lazy-loaded (Lesson 10.4).
- Each feature exposes a route file so the app can load it on demand:

```ts
// features/tasks/tasks.routes.ts
export const TASK_ROUTES: Routes = [
  { path: '', component: TaskListComponent },
  { path: ':id', component: TaskDetailComponent },
];
```

- Keep services in a feature folder if only that feature uses them. Put them in `core/` only when truly global.
- Avoid features importing each other's internals. If two features need the same thing, move it to `shared/` or `core/`.

A small project can start flat. Introduce folders when navigation starts to hurt.

---

## 8.4 Migration basics

Angular provides automated schematics to move an NgModule app to standalone. Run them in this order, committing to Git between steps:

```bash
ng generate @angular/core:standalone
```

The command asks which mode to run:

1. **Convert all components, directives, and pipes to standalone** - adds `standalone: true`, moves module imports into each component's `imports`.
2. **Remove unnecessary NgModules** - deletes modules that no longer do anything.
3. **Bootstrap the application using standalone APIs** - rewrites `main.ts` to `bootstrapApplication`.

After each step:

1. Run `ng build` and fix errors.
2. Run your tests.
3. Commit.

Then migrate other modern features if you wish:

```bash
ng generate @angular/core:control-flow     # *ngIf/*ngFor -> @if/@for
ng generate @angular/core:inject           # constructor injection -> inject()
```

Tips for a smooth migration:

- Upgrade to a recent Angular version first (`ng update`), so all schematics are available.
- Migrate **leaf** components first; the tool handles ordering for you in the multi-step flow.
- Third-party libraries that still ship NgModules continue to work. Import their modules inside standalone components.
- Review the diff by hand. Automatic migration is a strong start, not a guarantee.
- Migrating is optional and can be done gradually; standalone and NgModule code coexist.

---

**Further reading:** [Standalone migration](https://angular.dev/reference/migrations/standalone)

[Previous](./[7]-Services-And-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[9]-Lifecycle-Hooks.md)
