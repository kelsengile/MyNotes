[Previous](./[11]-Forms.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[13]-RxJS-And-Observables.md)

*Building Applications*

# Lesson 12 - HTTP And Interceptors

Most apps talk to a server. Angular's `HttpClient` sends requests and returns RxJS Observables. This lesson covers making requests, typing responses, intercepting every request, and handling failures.

---

## 12.1 HttpClient basics

Step 1: register the HTTP client once, in your app config.

```ts
import { provideHttpClient } from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [provideRouter(routes), provideHttpClient()],
};
```

Step 2: inject `HttpClient`, ideally inside a service so components never build URLs themselves.

```ts
import { HttpClient } from '@angular/common/http';
import { Injectable, inject } from '@angular/core';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class TaskApi {
  private http = inject(HttpClient);
  private baseUrl = 'https://api.example.com/tasks';

  list(): Observable<Task[]>               { return this.http.get<Task[]>(this.baseUrl); }
  get(id: number): Observable<Task>        { return this.http.get<Task>(`${this.baseUrl}/${id}`); }
  create(t: Partial<Task>): Observable<Task> { return this.http.post<Task>(this.baseUrl, t); }
  update(t: Task): Observable<Task>        { return this.http.put<Task>(`${this.baseUrl}/${t.id}`, t); }
  remove(id: number): Observable<void>     { return this.http.delete<void>(`${this.baseUrl}/${id}`); }
}
```

Step 3: use it. **A request is only sent when something subscribes.**

```ts
export class TaskListComponent {
  private api = inject(TaskApi);
  tasks = toSignal(this.api.list(), { initialValue: [] });   // subscribes for you
}
```

Or in the template with the `async` pipe, or manually with `.subscribe(...)`.

Common options:

```ts
import { HttpParams, HttpHeaders } from '@angular/common/http';

this.http.get<Task[]>(url, {
  params: new HttpParams().set('page', 2).set('sort', 'due'),   // ?page=2&sort=due
  headers: new HttpHeaders({ 'X-Request-Id': 'abc' }),
});
```

`HttpParams` and `HttpHeaders` are **immutable**: `set` returns a new object, so keep the result.

Observable details: an `HttpClient` call emits **one** value and completes, so you do not need to unsubscribe manually. Remember the browser's **CORS** rules: calling a different origin only works if the server allows it.

---

## 12.2 Typed requests and responses

The generic type on `get<T>` tells TypeScript what shape to expect, giving you autocompletion and compile-time checks.

```ts
export interface Task { id: number; title: string; done: boolean; dueDate: string; }

this.http.get<Task[]>(url).subscribe((tasks) => {
  console.log(tasks[0].title);   // typed
});
```

**Important:** the generic is a *promise you make*, not a check. If the server sends different JSON, TypeScript will not notice at runtime. For untrusted or complex APIs, validate with a schema library such as Zod, or map the response explicitly.

**Separating API shape from app shape** with `map`:

```ts
interface TaskDto { id: number; title: string; due_date: string; }

list(): Observable<Task[]> {
  return this.http.get<TaskDto[]>(this.baseUrl).pipe(
    map((dtos) => dtos.map((d) => ({ id: d.id, title: d.title, done: false, dueDate: d.due_date }))),
  );
}
```

Other response options:

```ts
this.http.get(url, { responseType: 'text' });                  // string instead of JSON
this.http.get<Task>(url, { observe: 'response' });             // full HttpResponse (status, headers)
this.http.get(url, { responseType: 'blob' });                  // files
```

**Tracking a loading state with signals:**

```ts
tasks = signal<Task[]>([]);
loading = signal(false);

load(): void {
  this.loading.set(true);
  this.api.list().pipe(finalize(() => this.loading.set(false))).subscribe((t) => this.tasks.set(t));
}
```

> `httpResource()` is a newer signal-based way to fetch data that exposes `value()`, `isLoading()`, and `error()` signals. Check the Angular docs for its current status before relying on it.

---

## 12.3 Interceptors

An **interceptor** is a function that sees every HTTP request and response. Use it for cross-cutting concerns: attaching auth tokens, logging, showing a global spinner, mapping errors.

Requests are **immutable**, so you `clone` them to change something.

```ts
import { HttpInterceptorFn } from '@angular/common/http';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const token = inject(AuthService).token();
  if (!token) return next(req);

  const authed = req.clone({ setHeaders: { Authorization: `Bearer ${token}` } });
  return next(authed);
};
```

Register interceptors with `withInterceptors`; they run in the listed order on the way out, and in reverse on the way back:

```ts
provideHttpClient(withInterceptors([authInterceptor, loggingInterceptor]))
```

An interceptor that also handles the response:

```ts
export const loggingInterceptor: HttpInterceptorFn = (req, next) => {
  const started = performance.now();
  return next(req).pipe(
    tap({
      next: (event) => {
        if (event instanceof HttpResponse) {
          console.log(`${req.method} ${req.url} -> ${event.status} (${Math.round(performance.now() - started)}ms)`);
        }
      },
    }),
  );
};
```

Practical advice:

- Only add the token for **your own API's URL**, never for third-party domains; otherwise you leak credentials.
- Keep each interceptor small and single-purpose.
- `inject()` works inside the function, because interceptors run in an injection context.
- Older projects use class-based interceptors implementing `HttpInterceptor` registered with `HTTP_INTERCEPTORS`. Enable them with `withInterceptorsFromDi()`.

---

## 12.4 Error handling and retries

Requests fail: the network drops, the server returns `500`, the token expires. Handle errors with RxJS operators.

```ts
import { catchError, retry, throwError, of, timer } from 'rxjs';
import { HttpErrorResponse } from '@angular/common/http';

list(): Observable<Task[]> {
  return this.http.get<Task[]>(this.baseUrl).pipe(
    retry(2),                                   // try up to 2 more times
    catchError((err: HttpErrorResponse) => {
      console.error(err.status, err.message);
      return throwError(() => new Error('Could not load tasks.'));
    }),
  );
}
```

`HttpErrorResponse` fields:

| Field | Meaning |
|---|---|
| `status` | HTTP status code (`0` means network failure or blocked by CORS) |
| `statusText`, `message` | Text descriptions |
| `error` | The response body the server sent |

**Handle by status, ideally in one interceptor:**

```ts
export const errorInterceptor: HttpInterceptorFn = (req, next) =>
  next(req).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) inject(Router).navigate(['/login']);
      return throwError(() => err);
    }),
  );
```

**Smarter retry with a delay:**

```ts
retry({ count: 3, delay: (_err, attempt) => timer(attempt * 1000) })   // 1s, 2s, 3s
```

Guidelines:

- **Retry only safe, idempotent requests** (`GET`). Retrying a `POST` can create duplicates.
- Do not retry `4xx` client errors such as `400`, `401`, `404`; they will not fix themselves.
- `catchError` must return an Observable: `throwError(...)` to rethrow, or `of(fallback)` to recover with a default.
- Show the user something: a message, a retry button, or an empty state. A silent failure is the worst result.

---

**Further reading:** [HttpClient guide](https://angular.dev/guide/http)

[Previous](./[11]-Forms.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[13]-RxJS-And-Observables.md)
