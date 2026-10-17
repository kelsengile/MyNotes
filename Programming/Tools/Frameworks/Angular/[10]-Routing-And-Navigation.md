[Previous](./[9]-Lifecycle-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[11]-Forms.md)

*Building Applications*

# Lesson 10 - Routing And Navigation

In a single-page app the **router** swaps components in and out as the URL changes, without reloading the page. This lesson covers defining routes, reading parameters, protecting routes, and loading code on demand.

---

## 10.1 Configuring routes

Step 1: define routes as an array that maps URL paths to components.

```ts
// app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', component: HomeComponent, title: 'Home' },
  { path: 'tasks', component: TaskListComponent, title: 'Tasks' },
  { path: 'tasks/:id', component: TaskDetailComponent },
  { path: 'about', redirectTo: '', pathMatch: 'full' },
  { path: '**', component: NotFoundComponent },   // wildcard: must be last
];
```

Step 2: register the router.

```ts
// app.config.ts
export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes)],
};
```

Step 3: add an outlet where the routed component should appear, and links to navigate.

```ts
import { RouterOutlet, RouterLink, RouterLinkActive } from '@angular/router';

@Component({
  selector: 'app-root',
  imports: [RouterOutlet, RouterLink, RouterLinkActive],
  template: `
    <nav>
      <a routerLink="/" routerLinkActive="active" [routerLinkActiveOptions]="{ exact: true }">Home</a>
      <a routerLink="/tasks" routerLinkActive="active">Tasks</a>
    </nav>
    <router-outlet />
  `,
})
export class AppComponent {}
```

Rules to know:

- Routes are matched **top to bottom**; the first match wins. Put specific paths first and `**` last.
- Use `routerLink`, not `href`, for in-app links. A plain `href` causes a full page reload.
- `routerLinkActive` adds a CSS class to the link of the current page.

**Nested (child) routes** render inside a parent's own `<router-outlet>`:

```ts
{
  path: 'settings',
  component: SettingsLayoutComponent,
  children: [
    { path: 'profile', component: ProfileSettingsComponent },
    { path: 'security', component: SecuritySettingsComponent },
  ],
}
```

**Navigating from code:**

```ts
private router = inject(Router);

save(): void {
  this.router.navigate(['/tasks', 42]);                 // /tasks/42
  this.router.navigateByUrl('/tasks?sort=due');
}
```

---

## 10.2 Route and query parameters

- **Route parameters** are part of the path: `/tasks/42`, declared as `tasks/:id`.
- **Query parameters** come after `?`: `/tasks?sort=due&page=2`.

**Reading them with component inputs** (simplest, enable once in config):

```ts
provideRouter(routes, withComponentInputBinding())
```

```ts
@Component({
  selector: 'app-task-detail',
  template: `<h2>Task {{ id() }}</h2><p>Sorted by {{ sort() }}</p>`,
})
export class TaskDetailComponent {
  id = input.required<string>();     // route param "id"
  sort = input<string>();            // query param "sort" (optional)
}
```

The router fills inputs whose names match route params, query params, or resolved data. Params always arrive as **strings**, so convert with `Number(this.id())` or an input `transform`.

**Reading them with `ActivatedRoute`** (classic way, works everywhere):

```ts
private route = inject(ActivatedRoute);

// snapshot: value at the moment of creation only
const id = this.route.snapshot.paramMap.get('id');

// observable: updates if the URL changes while the component stays alive
id$ = this.route.paramMap.pipe(map((p) => p.get('id')));
```

**Why the observable matters:** navigating from `/tasks/1` to `/tasks/2` **reuses** the same component instance. A snapshot read in `ngOnInit` would stay stuck on `1`.

**Building links with parameters:**

```html
<a [routerLink]="['/tasks', task.id]" [queryParams]="{ tab: 'notes' }">Open</a>
```

Update only the query string without changing the route:

```ts
this.router.navigate([], { queryParams: { sort: 'due' }, queryParamsHandling: 'merge' });
```

---

## 10.3 Guards and resolvers

**Guards** are functions that decide whether navigation is allowed.

| Guard | Question it answers |
|---|---|
| `canActivate` | May the user enter this route? |
| `canActivateChild` | May the user enter any child of this route? |
| `canDeactivate` | May the user leave (e.g. unsaved changes)? |
| `canMatch` | Should this route even be considered (and its code loaded)? |

A functional guard (the modern style) uses `inject()`:

```ts
import { CanActivateFn, Router } from '@angular/router';
import { inject } from '@angular/core';

export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);

  return auth.isLoggedIn()
    ? true
    : router.createUrlTree(['/login'], { queryParams: { returnUrl: state.url } });
};
```

Return values: `true` to allow, `false` to cancel, or a `UrlTree` to **redirect**. A guard can also return an `Observable` or `Promise` of those values.

```ts
{ path: 'admin', component: AdminComponent, canActivate: [authGuard] }
```

A **resolver** fetches data **before** the route activates, so the component starts with its data ready:

```ts
import { ResolveFn } from '@angular/router';

export const taskResolver: ResolveFn<Task> = (route) => {
  return inject(TaskService).getById(route.paramMap.get('id')!);
};

{ path: 'tasks/:id', component: TaskDetailComponent, resolve: { task: taskResolver } }
```

With `withComponentInputBinding()`, the resolved value arrives as an input called `task`. Resolvers delay navigation until they finish, so use them for essential data. For slower or optional data, load inside the component and show a loading state.

> **Security note:** guards only improve the user experience. Anyone can modify frontend code, so the server must enforce the real authorization.

---

## 10.4 Lazy loading

By default all code ships in one bundle. **Lazy loading** splits code so a route's files download only when the user visits it, making the first load faster.

**Lazy component:**

```ts
{
  path: 'reports',
  loadComponent: () =>
    import('./reports/reports.component').then((m) => m.ReportsComponent),
}
```

**Lazy group of routes** (a whole feature):

```ts
{
  path: 'tasks',
  loadChildren: () => import('./features/tasks/tasks.routes').then((m) => m.TASK_ROUTES),
}
```

With a default export, it shortens to `loadChildren: () => import('./tasks.routes')`.

Tips:

- Lazy-load whole **features**, especially rarely visited or heavy ones (admin, reports, editors).
- Do not lazy-load the home page; it is needed immediately.
- Services registered in a lazy route's `providers` exist only after that route loads.
- **Preloading** downloads lazy routes in the background after startup: `provideRouter(routes, withPreloading(PreloadAllModules))`.
- Check the result: `ng build` prints separate `lazy chunk` files for each lazy route.
- Older NgModule apps used `loadChildren: () => import('./x.module').then((m) => m.XModule)`.

---

**Further reading:** [Routing guide](https://angular.dev/guide/routing)

[Previous](./[9]-Lifecycle-Hooks.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[11]-Forms.md)
