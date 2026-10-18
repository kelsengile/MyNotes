[Previous](./[5]-Providers-And-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[7]-Pipes-And-Validation.md)

*The Request Pipeline*

# Lesson 6 - Middleware

## 6.1 Function and class middleware

**Middleware** runs **before** the route handler. It can read and change the request and response, end the request early, or call `next()` to pass control forward. Nest middleware is equivalent to Express middleware.

### Function middleware

Simple and stateless:

```ts
import { Request, Response, NextFunction } from 'express';

export function logger(req: Request, res: Response, next: NextFunction) {
  console.log(`${req.method} ${req.originalUrl}`);
  next();
}
```

### Class middleware

Use a class when you need dependency injection:

```ts
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  constructor(private readonly clock: ClockService) {}

  use(req: Request, res: Response, next: NextFunction) {
    console.log(`[${this.clock.now()}] ${req.method} ${req.originalUrl}`);
    next();
  }
}
```

If middleware does not call `next()` and does not send a response, the request will hang.

---

## 6.2 Applying middleware

Middleware is not listed in `@Module()`. Instead, the module implements `NestModule` and configures it:

```ts
import { MiddlewareConsumer, Module, NestModule, RequestMethod } from '@nestjs/common';

@Module({
  controllers: [CatsController],
})
export class CatsModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes(CatsController); // all routes of this controller
  }
}
```

`forRoutes()` accepts several forms:

```ts
.forRoutes(CatsController)                                   // a controller
.forRoutes({ path: 'cats', method: RequestMethod.GET })      // a path + method
.forRoutes('cats')                                           // a path
```

Exclude routes:

```ts
consumer
  .apply(LoggerMiddleware)
  .exclude({ path: 'cats/health', method: RequestMethod.GET })
  .forRoutes(CatsController);
```

Apply several middleware in order by listing them: `.apply(cors(), helmet(), LoggerMiddleware)`.

---

## 6.3 Global middleware

To bind middleware to every route, use `app.use()` in `main.ts`. This works with **function** middleware (it has no dependency injection):

```ts
// main.ts
async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  app.use(logger);
  await app.listen(3000);
}
```

Many third-party packages are used this way, for example `app.use(helmet())`.

To use a **class** middleware globally with DI, apply it in the root module's `configure()` for all routes of your controllers instead.

---

## 6.4 Use cases

Middleware is best for low-level HTTP concerns that apply broadly:

- **Request logging** and timing
- **Request IDs** attached to every request for tracing
- **Parsing** or normalizing headers and cookies
- **Security headers** (Helmet) and CORS
- **Compression** and body-size limits

Example: adding a request ID.

```ts
import { randomUUID } from 'node:crypto';

export function requestId(req: Request, res: Response, next: NextFunction) {
  const id = (req.headers['x-request-id'] as string) ?? randomUUID();
  res.setHeader('x-request-id', id);
  (req as any).id = id;
  next();
}
```

Middleware **does not know which route handler will run**, since it executes before routing decisions are made available. For work that depends on the handler (permissions, response transformation, validation), use **guards, interceptors, and pipes** (next lessons).

---

[Previous](./[5]-Providers-And-Dependency-Injection.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[7]-Pipes-And-Validation.md)