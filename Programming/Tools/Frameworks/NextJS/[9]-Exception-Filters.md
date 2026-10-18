[Previous](./[8]-Guards-And-Interceptors.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[10]-Request-Lifecycle.md)

*The Request Pipeline*

# Lesson 9 - Exception Filters

## 9.1 Built-in exceptions

When something goes wrong, you **throw** an exception. Nest has a built-in layer that catches exceptions and turns them into HTTP responses.

The base class is `HttpException`:

```ts
import { HttpException, HttpStatus } from '@nestjs/common';

throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
```

Nest provides ready-made subclasses so you rarely need the base class:

| Exception | Status |
|---|---|
| `BadRequestException` | 400 |
| `UnauthorizedException` | 401 |
| `ForbiddenException` | 403 |
| `NotFoundException` | 404 |
| `ConflictException` | 409 |
| `UnprocessableEntityException` | 422 |
| `InternalServerErrorException` | 500 |
| `ServiceUnavailableException` | 503 |

```ts
@Get(':id')
async findOne(@Param('id') id: string) {
  const user = await this.usersService.findOne(id);
  if (!user) throw new NotFoundException(`User ${id} not found`);
  return user;
}
```

The default response body looks like this:

```json
{ "statusCode": 404, "message": "User 7 not found", "error": "Not Found" }
```

Any error that is **not** an `HttpException` (for example a plain `Error`) becomes a generic `500 Internal Server Error`, and the details are hidden from the client.

---

## 9.2 Custom exceptions

Create your own exceptions by extending `HttpException` (or a built-in one). This gives business errors meaningful names:

```ts
import { HttpException, HttpStatus } from '@nestjs/common';

export class InsufficientFundsException extends HttpException {
  constructor(balance: number, required: number) {
    super(
      {
        statusCode: HttpStatus.UNPROCESSABLE_ENTITY,
        error: 'Insufficient Funds',
        message: `Balance ${balance} is lower than required ${required}`,
      },
      HttpStatus.UNPROCESSABLE_ENTITY,
    );
  }
}
```

```ts
if (account.balance < amount) {
  throw new InsufficientFundsException(account.balance, amount);
}
```

A good pattern: services throw meaningful exceptions, controllers stay clean.

---

## 9.3 Exception filters

An **exception filter** gives you full control over what the error response looks like. It is a class decorated with `@Catch()` that implements `ExceptionFilter`.

```ts
import { ArgumentsHost, Catch, ExceptionFilter, HttpException } from '@nestjs/common';
import { Request, Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const request = ctx.getRequest<Request>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      path: request.url,
      error: exception.getResponse(),
    });
  }
}
```

`@Catch(HttpException)` limits the filter to that exception type. Use `@Catch()` with no arguments to catch **everything**.

Bind filters on a method, a controller, or globally:

```ts
@UseFilters(HttpExceptionFilter)   // method or controller
app.useGlobalFilters(new HttpExceptionFilter()); // whole app (no DI)
```

Providing a global filter **with dependency injection** is done through a module:

```ts
import { APP_FILTER } from '@nestjs/core';

@Module({
  providers: [{ provide: APP_FILTER, useClass: HttpExceptionFilter }],
})
export class AppModule {}
```

---

## 9.4 Consistent error responses

Clients love predictable errors. A catch-all filter can ensure every error, including unexpected ones, has the same shape and does not leak internals.

```ts
import {
  ArgumentsHost, Catch, ExceptionFilter, HttpException, HttpStatus, Logger,
} from '@nestjs/common';
import { HttpAdapterHost } from '@nestjs/core';

@Catch()
export class AllExceptionsFilter implements ExceptionFilter {
  private readonly logger = new Logger(AllExceptionsFilter.name);

  constructor(private readonly httpAdapterHost: HttpAdapterHost) {}

  catch(exception: unknown, host: ArgumentsHost) {
    const { httpAdapter } = this.httpAdapterHost;
    const ctx = host.switchToHttp();

    const isHttp = exception instanceof HttpException;
    const status = isHttp ? exception.getStatus() : HttpStatus.INTERNAL_SERVER_ERROR;
    const message = isHttp ? exception.message : 'Internal server error';

    if (!isHttp) {
      // Log the real error on the server, never send it to the client
      this.logger.error(exception instanceof Error ? exception.stack : String(exception));
    }

    httpAdapter.reply(
      ctx.getResponse(),
      {
        statusCode: status,
        message,
        path: httpAdapter.getRequestUrl(ctx.getRequest()),
        timestamp: new Date().toISOString(),
      },
      status,
    );
  }
}
```

Register it with `APP_FILTER` so that `HttpAdapterHost` is injected:

```ts
providers: [{ provide: APP_FILTER, useClass: AllExceptionsFilter }]
```

Guidelines:

- Always **log** unexpected errors server-side.
- **Never** return stack traces or database errors to clients in production.
- Use one error shape across the whole API.

---

[Previous](./[8]-Guards-And-Interceptors.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[10]-Request-Lifecycle.md)