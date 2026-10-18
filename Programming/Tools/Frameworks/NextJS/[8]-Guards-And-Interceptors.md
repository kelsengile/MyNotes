[Previous](./[7]-Pipes-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[9]-Exception-Filters.md)

*The Request Pipeline*

# Lesson 8 - Guards And Interceptors

## 8.1 Guards for access control

A **guard** decides whether a request is allowed to reach the handler. It implements `CanActivate` and returns `true` (allow) or `false` (deny). Returning `false` makes Nest respond with `403 Forbidden`. Throwing an exception lets you choose the status, such as `401`.

```ts
import { CanActivate, ExecutionContext, Injectable, UnauthorizedException } from '@nestjs/common';
import { Request } from 'express';

@Injectable()
export class ApiKeyGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest<Request>();
    const key = request.headers['x-api-key'];

    if (key !== process.env.API_KEY) {
      throw new UnauthorizedException('Invalid API key');
    }
    return true;
  }
}
```

Apply guards at three levels:

```ts
// 1. A single route
@UseGuards(ApiKeyGuard)
@Get()
findAll() {}

// 2. A whole controller
@UseGuards(ApiKeyGuard)
@Controller('cats')
export class CatsController {}

// 3. The entire app
app.useGlobalGuards(new ApiKeyGuard());
```

Guards run **after middleware** and **before pipes and interceptors**. Unlike middleware, guards know exactly which handler will run, which makes them ideal for authentication and authorization (Lessons 14 and 15).

---

## 8.2 Interceptors for transformation

An **interceptor** wraps the handler. It can run code **before** the handler, **after** it, transform the result, transform errors, or even replace the handler's result. It implements `NestInterceptor` and uses RxJS.

```ts
import { CallHandler, ExecutionContext, Injectable, NestInterceptor } from '@nestjs/common';
import { Observable } from 'rxjs';
import { map, tap } from 'rxjs/operators';

// Log how long each request takes
@Injectable()
export class TimingInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    const start = Date.now();
    return next.handle().pipe(
      tap(() => console.log(`Took ${Date.now() - start}ms`)),
    );
  }
}

// Wrap every response in { data: ... }
@Injectable()
export class WrapResponseInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(map((data) => ({ data })));
  }
}
```

The key line is `next.handle()`: everything **before** it runs before the handler, and operators in `.pipe()` run **after** the handler produces its result. If you never call `next.handle()`, the handler never runs.

Bind interceptors like guards: `@UseInterceptors(TimingInterceptor)` on a method or controller, or `app.useGlobalInterceptors(...)`.

Common uses: logging, response mapping, caching, timeouts, and retries.

```ts
import { timeout, catchError } from 'rxjs/operators';
import { throwError, TimeoutError } from 'rxjs';
import { RequestTimeoutException } from '@nestjs/common';

@Injectable()
export class TimeoutInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler) {
    return next.handle().pipe(
      timeout(5000),
      catchError((err) =>
        err instanceof TimeoutError
          ? throwError(() => new RequestTimeoutException())
          : throwError(() => err),
      ),
    );
  }
}
```

---

## 8.3 Execution context

Both guards and interceptors receive an `ExecutionContext`, which describes the current call. It works for HTTP, WebSockets, GraphQL, and microservices, so it first asks which type you are in:

```ts
const http = context.switchToHttp();
const request = http.getRequest();
const response = http.getResponse();

const type = context.getType(); // 'http' | 'ws' | 'rpc' | 'graphql'
```

It also tells you **which handler and class** is running, which lets you read metadata:

```ts
context.getHandler(); // the method about to run
context.getClass();   // the controller class
```

Combine this with the `Reflector` helper and custom metadata decorators to build flexible guards:

```ts
import { SetMetadata } from '@nestjs/common';
export const IS_PUBLIC = 'isPublic';
export const Public = () => SetMetadata(IS_PUBLIC, true);
```

```ts
import { Reflector } from '@nestjs/core';

@Injectable()
export class ExampleGuard implements CanActivate {
  constructor(private reflector: Reflector) {}

  canActivate(context: ExecutionContext): boolean {
    const isPublic = this.reflector.getAllAndOverride<boolean>(IS_PUBLIC, [
      context.getHandler(),
      context.getClass(),
    ]);
    return isPublic ? true : this.checkSomething(context);
  }

  private checkSomething(context: ExecutionContext) {
    return true;
  }
}
```

`getAllAndOverride` looks at the handler first, then the class, and returns the first value found.

---

## 8.4 Combining them

A single endpoint can use several of these tools, each with a clear job:

```ts
@Controller('reports')
@UseGuards(ApiKeyGuard)                 // 1. Who may call this?
@UseInterceptors(WrapResponseInterceptor, TimingInterceptor) // 3. Shape and measure the response
export class ReportsController {
  @Post()
  create(@Body() dto: CreateReportDto) { // 2. ValidationPipe checks input
    return this.reportsService.create(dto);
  }
}
```

| Tool | Question it answers |
|---|---|
| Middleware | Should this raw HTTP request be adjusted or logged? |
| Guard | Is this caller allowed? |
| Interceptor | How should the call be wrapped, timed, cached, or its result reshaped? |
| Pipe | Is this input valid and in the right type? |

Interceptors listed in a decorator run in order on the way in, and in reverse order on the way out. The next lessons finish the picture with exception filters and the full request lifecycle.

---

[Previous](./[7]-Pipes-And-Validation.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[9]-Exception-Filters.md)