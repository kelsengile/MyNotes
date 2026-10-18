[Previous](./[3]-Modules.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[5]-Providers-And-Dependency-Injection.md)

*Basics*

# Lesson 4 - Controllers And Routing

## 4.1 Defining controllers

A **controller** receives incoming requests and returns responses. It should stay thin: read the request, call a service, return the result.

```ts
import { Controller, Get } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  @Get()
  findAll(): string {
    return 'This returns all cats';
  }
}
```

The string passed to `@Controller('cats')` is the **route prefix**. `@Get()` with no argument maps to `GET /cats`.

A controller must be listed in a module's `controllers` array, or Nest will not know it exists:

```ts
@Module({
  controllers: [CatsController],
})
export class CatsModule {}
```

---

## 4.2 Route handlers and decorators

Each method decorated with an HTTP-method decorator is a **route handler**. Available decorators: `@Get()`, `@Post()`, `@Put()`, `@Patch()`, `@Delete()`, `@Options()`, `@Head()`, and `@All()`.

```ts
@Controller('cats')
export class CatsController {
  @Get()          // GET    /cats
  findAll() {}

  @Get(':id')     // GET    /cats/5
  findOne() {}

  @Post()         // POST   /cats
  create() {}

  @Patch(':id')   // PATCH  /cats/5
  update() {}

  @Delete(':id')  // DELETE /cats/5
  remove() {}
}
```

**Route order matters.** Nest matches in the order handlers are declared, so a dynamic route like `:id` placed first will swallow a static route such as `featured`.

```ts
@Get('featured') // declare static routes BEFORE
featured() {}

@Get(':id')      // dynamic routes
findOne() {}
```

Handlers can be `async` and can return a `Promise` or an RxJS `Observable`. Nest waits for the result:

```ts
@Get()
async findAll(): Promise<Cat[]> {
  return this.catsService.findAll();
}
```

---

## 4.3 Parameters, queries, and bodies

Use parameter decorators to read parts of the request.

| Decorator | Reads |
|---|---|
| `@Param('id')` | Route parameter, e.g. `/cats/:id` |
| `@Query('page')` | Query string, e.g. `?page=2` |
| `@Body()` | Request body |
| `@Headers('authorization')` | A request header |
| `@Req()` | The raw request object |

```ts
import { Body, Controller, Get, Param, Post, Query } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  // GET /cats?breed=siamese&limit=10
  @Get()
  findAll(@Query('breed') breed?: string, @Query('limit') limit?: string) {
    return { breed, limit };
  }

  // GET /cats/5
  @Get(':id')
  findOne(@Param('id') id: string) {
    return `Cat #${id}`;
  }

  // POST /cats with a JSON body
  @Post()
  create(@Body() createCatDto: CreateCatDto) {
    return createCatDto;
  }
}
```

Route and query values arrive as **strings**. Lesson 7 shows how pipes convert them to numbers automatically.

A **DTO (Data Transfer Object)** describes the shape of a request body. Use a **class**, not an interface, so it survives to runtime:

```ts
export class CreateCatDto {
  name: string;
  age: number;
  breed: string;
}
```

---

## 4.4 Status codes and responses

Nest picks sensible defaults: `200` for most responses and `201` for `@Post()`. Change them with `@HttpCode()`:

```ts
import { HttpCode, Header, Redirect, Post, Get } from '@nestjs/common';

@Post()
@HttpCode(204)
create() {}

@Get('docs')
@Header('Cache-Control', 'no-store')
docs() {
  return 'docs';
}

@Get('go')
@Redirect('https://nestjs.com', 302)
goToNest() {}
```

To signal an error, **throw** an exception (covered fully in Lesson 9):

```ts
import { NotFoundException } from '@nestjs/common';

@Get(':id')
findOne(@Param('id') id: string) {
  const cat = this.catsService.findOne(id);
  if (!cat) {
    throw new NotFoundException(`Cat ${id} not found`);
  }
  return cat;
}
```

By default Nest sends what you `return`. If you inject the underlying response object with `@Res()`, **you** become responsible for sending the response, and Nest stops handling it. To use `@Res()` but keep Nest's normal behavior, add `passthrough`:

```ts
import { Res } from '@nestjs/common';
import type { Response } from 'express';

@Get()
findAll(@Res({ passthrough: true }) res: Response) {
  res.setHeader('X-Total-Count', '42');
  return this.catsService.findAll(); // Nest still serializes this
}
```

Prefer returning values and Nest's decorators, and use `@Res()` only when you truly need low-level control.

---

[Previous](./[3]-Modules.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[5]-Providers-And-Dependency-Injection.md)