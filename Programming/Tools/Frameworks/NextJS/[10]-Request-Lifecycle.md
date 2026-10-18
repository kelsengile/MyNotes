[Previous](./[9]-Exception-Filters.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[11]-Configuration-And-Environments.md)

*The Request Pipeline*

# Lesson 10 - Request Lifecycle

## 10.1 Order of execution

Every request passes through the same stages in the same order. Knowing this order explains most "why did that run first?" questions.

1. **Incoming request**
2. **Middleware**
   1. Global middleware (`app.use()`)
   2. Module middleware (`configure()`)
3. **Guards**
   1. Global guards
   2. Controller guards
   3. Route guards
4. **Interceptors (before the handler)**
   1. Global
   2. Controller
   3. Route
5. **Pipes**
   1. Global
   2. Controller
   3. Route
   4. Route parameter pipes
6. **Controller method (the handler)**, which usually calls a service
7. **Interceptors (after the handler)**, in reverse order: route, controller, global
8. **Exception filters** (only if something threw): route, controller, global. The first that matches the exception handles it.
9. **Server response**

A visual summary:

```
Request
  -> Middleware
  -> Guards
  -> Interceptors (in)
  -> Pipes
  -> Handler
  -> Interceptors (out)
  -> Response

(An exception thrown anywhere jumps to Exception Filters)
```

---

## 10.2 Where each piece fits

| Piece | Runs | Best for | Can access |
|---|---|---|---|
| Middleware | First | Logging, request IDs, security headers | Raw request/response |
| Guard | After middleware | Authentication, authorization | Execution context and handler metadata |
| Interceptor | Around the handler | Timing, caching, response mapping | Context, plus the handler's result |
| Pipe | Right before the handler | Validation and parsing of arguments | The individual argument value |
| Exception filter | On error | Formatting error responses | The exception and context |

Rule of thumb for choosing:

- Need to **allow or block** someone? Guard.
- Need to **check or convert input**? Pipe.
- Need to **change the output** or measure the call? Interceptor.
- Need to **adjust the raw HTTP layer**? Middleware.
- Need to **shape the error**? Exception filter.

Exceptions thrown inside guards, pipes, interceptors, and handlers are all caught by exception filters. Errors thrown in middleware are also handled by Nest's exception layer, since they occur within the request pipeline.

---

## 10.3 Debugging the pipeline

When something behaves unexpectedly, trace the order by logging in each stage:

```ts
// middleware
use(req, res, next) { console.log('1. middleware'); next(); }

// guard
canActivate() { console.log('2. guard'); return true; }

// interceptor
intercept(ctx, next) {
  console.log('3. interceptor (before)');
  return next.handle().pipe(tap(() => console.log('6. interceptor (after)')));
}

// pipe
transform(value) { console.log('4. pipe'); return value; }

// handler
handler() { console.log('5. handler'); return 'ok'; }
```

Expected console output for one request:

```
1. middleware
2. guard
3. interceptor (before)
4. pipe
5. handler
6. interceptor (after)
```

Common surprises and their causes:

| Symptom | Likely cause |
|---|---|
| Guard cannot see parsed/validated body | Pipes run **after** guards |
| DTO validation never runs | `ValidationPipe` is not registered globally or on the route |
| Global filter has no injected services | Registered with `app.useGlobalFilters(new X())` instead of `APP_FILTER` |
| Wrong route matched | A dynamic `:id` route is declared before a static one |
| `req.user` is undefined | The auth guard did not run, or it is applied after your code reads it |

Enable verbose logs with `NestFactory.create(AppModule, { logger: ['log', 'debug', 'error', 'warn', 'verbose'] })`.

---

## 10.4 Design guidelines

- **Keep each stage focused.** One class should do one job (validate, authorize, log).
- **Prefer global registration** for app-wide rules (validation, error format) and **narrow scope** for exceptions to the rule.
- **Use `APP_GUARD`, `APP_INTERCEPTOR`, `APP_PIPE`, `APP_FILTER`** tokens in modules when your global pieces need dependency injection.
- **Do not put business logic** in guards, pipes, or interceptors. Services own business logic.
- **Fail early.** Authentication and validation should reject bad requests before they reach expensive code.
- **Throw meaningful exceptions** and let a single filter format them.

```ts
@Module({
  providers: [
    { provide: APP_GUARD, useClass: JwtAuthGuard },
    { provide: APP_PIPE, useValue: new ValidationPipe({ whitelist: true, transform: true }) },
    { provide: APP_INTERCEPTOR, useClass: TimingInterceptor },
    { provide: APP_FILTER, useClass: AllExceptionsFilter },
  ],
})
export class AppModule {}
```

---

[Previous](./[9]-Exception-Filters.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[11]-Configuration-And-Environments.md)