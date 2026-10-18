[Previous](./[6]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[8]-Guards-And-Interceptors.md)

*The Request Pipeline*

# Lesson 7 - Pipes And Validation

## 7.1 Built-in pipes

A **pipe** runs just before a handler and does one of two jobs:

- **Transformation:** convert input (for example, string `"5"` to number `5`).
- **Validation:** check input and throw an exception if it is invalid.

Nest includes several built-in pipes:

| Pipe | Purpose |
|---|---|
| `ParseIntPipe` | Converts to an integer |
| `ParseFloatPipe` | Converts to a float |
| `ParseBoolPipe` | Converts `"true"`/`"false"` to boolean |
| `ParseUUIDPipe` | Validates a UUID |
| `ParseEnumPipe` | Validates against an enum |
| `DefaultValuePipe` | Provides a default when a value is missing |
| `ValidationPipe` | Validates DTO classes |

```ts
import { Controller, Get, Param, ParseIntPipe, Query, DefaultValuePipe } from '@nestjs/common';

@Controller('cats')
export class CatsController {
  // GET /cats/abc -> 400 Bad Request ("Validation failed (numeric string is expected)")
  @Get(':id')
  findOne(@Param('id', ParseIntPipe) id: number) {
    return `Cat #${id}`; // id is a real number here
  }

  // GET /cats?page=2   (page defaults to 1)
  @Get()
  findAll(@Query('page', new DefaultValuePipe(1), ParseIntPipe) page: number) {
    return { page };
  }
}
```

---

## 7.2 DTOs

A **DTO** defines what data an endpoint expects. Use a class so Nest can inspect it at runtime.

```ts
export class CreateUserDto {
  name: string;
  email: string;
  age: number;
}
```

On its own this only provides type hints. Adding validation decorators makes the DTO enforceable.

---

## 7.3 class-validator and class-transformer

Install both libraries:

```bash
npm install class-validator class-transformer
```

Add rules to the DTO with decorators:

```ts
import { IsEmail, IsInt, IsOptional, IsString, Max, Min, MinLength } from 'class-validator';

export class CreateUserDto {
  @IsString()
  @MinLength(2)
  name: string;

  @IsEmail()
  email: string;

  @IsInt()
  @Min(0)
  @Max(120)
  age: number;

  @IsOptional()
  @IsString()
  nickname?: string;
}
```

Use it in a controller:

```ts
@Post()
create(@Body() dto: CreateUserDto) {
  return dto;
}
```

With `ValidationPipe` enabled (see 7.4), invalid input returns a `400` response listing every problem:

```json
{
  "message": ["name must be longer than or equal to 2 characters", "email must be an email"],
  "error": "Bad Request",
  "statusCode": 400
}
```

For update endpoints, build a DTO with all fields optional using mapped types:

```bash
npm install @nestjs/mapped-types
```

```ts
import { PartialType } from '@nestjs/mapped-types';

export class UpdateUserDto extends PartialType(CreateUserDto) {}
```

`class-transformer` converts plain JSON into class instances (and nested objects with `@Type()`), which is needed for features like `transform: true`:

```ts
import { Type } from 'class-transformer';
import { ValidateNested } from 'class-validator';

export class CreateOrderDto {
  @ValidateNested()
  @Type(() => AddressDto)
  address: AddressDto;
}
```

---

## 7.4 Global validation

Enable validation for the whole app in `main.ts`:

```ts
import { ValidationPipe } from '@nestjs/common';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  app.useGlobalPipes(
    new ValidationPipe({
      whitelist: true,            // strip properties not in the DTO
      forbidNonWhitelisted: true, // reject requests that include extra properties
      transform: true,            // convert payloads and params to DTO/primitive types
    }),
  );

  await app.listen(3000);
}
bootstrap();
```

What each option does:

| Option | Effect |
|---|---|
| `whitelist` | Silently removes unknown properties (protects against mass assignment) |
| `forbidNonWhitelisted` | Returns an error for unknown properties instead of stripping them |
| `transform` | Turns plain objects into DTO instances and converts primitive types for `@Param()`/`@Query()` |

You can also write a **custom pipe** by implementing `PipeTransform`:

```ts
import { ArgumentMetadata, BadRequestException, Injectable, PipeTransform } from '@nestjs/common';

@Injectable()
export class TrimPipe implements PipeTransform<string, string> {
  transform(value: string, metadata: ArgumentMetadata) {
    if (typeof value !== 'string') {
      throw new BadRequestException('Expected a string');
    }
    return value.trim();
  }
}

// Usage: @Body('title', TrimPipe) title: string
```

---

[Previous](./[6]-Middleware.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[8]-Guards-And-Interceptors.md)