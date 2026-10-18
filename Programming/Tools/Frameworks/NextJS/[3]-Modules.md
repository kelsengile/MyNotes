[Previous](./[2]-TypeScript-And-Decorators.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[4]-Controllers-And-Routing.md)

*Basics*

# Lesson 3 - Modules

## 3.1 The module system

A **module** is a class decorated with `@Module()`. It groups related controllers and providers into one unit. Every Nest app has a **root module**, and other modules connect to it through `imports`, forming a tree.

```ts
import { Module } from '@nestjs/common';

@Module({
  imports: [],      // other modules whose exported providers we need
  controllers: [],  // controllers that belong to this module
  providers: [],    // services/providers created by this module
  exports: [],      // providers we make available to importing modules
})
export class ExampleModule {}
```

| Property | Meaning |
|---|---|
| `imports` | Modules this module depends on |
| `controllers` | Controllers that handle requests for this module |
| `providers` | Classes Nest should instantiate and inject |
| `exports` | The subset of providers other modules may use |

**Encapsulation** is the key rule: a provider is private to its module unless it is listed in `exports`, and another module must `import` that module to use it.

---

## 3.2 Feature modules

Group everything for one feature (users, cats, orders) in its own module.

```ts
// src/users/users.module.ts
import { Module } from '@nestjs/common';
import { UsersController } from './users.controller';
import { UsersService } from './users.service';

@Module({
  controllers: [UsersController],
  providers: [UsersService],
})
export class UsersModule {}
```

Then register it in the root module:

```ts
// src/app.module.ts
import { Module } from '@nestjs/common';
import { UsersModule } from './users/users.module';

@Module({
  imports: [UsersModule],
})
export class AppModule {}
```

Generate this structure quickly with `nest g module users`. The CLI updates `AppModule` for you.

A typical feature folder:

```
src/users/
├── dto/
├── entities/
├── users.controller.ts
├── users.module.ts
└── users.service.ts
```

---

## 3.3 Shared and global modules

### Shared modules

To let other modules use a provider, **export** it, and have them **import** the module.

```ts
// users.module.ts
@Module({
  providers: [UsersService],
  exports: [UsersService], // now other modules can use it
})
export class UsersModule {}
```

```ts
// auth.module.ts
@Module({
  imports: [UsersModule], // gives AuthModule access to UsersService
  providers: [AuthService],
})
export class AuthModule {}
```

```ts
// auth.service.ts
@Injectable()
export class AuthService {
  constructor(private readonly usersService: UsersService) {}
}
```

Providers are **singletons** by default, so every module that imports `UsersModule` shares the same `UsersService` instance.

### Global modules

If many modules need the same provider (for example configuration or a database helper), mark the module `@Global()`. Its exports are then available everywhere without importing it.

```ts
import { Global, Module } from '@nestjs/common';

@Global()
@Module({
  providers: [LoggerService],
  exports: [LoggerService],
})
export class LoggerModule {}
```

Register a global module **once**, usually in `AppModule`. Use `@Global()` sparingly, because explicit imports make dependencies easier to see.

---

## 3.4 Dynamic modules

A **dynamic module** is configured when imported. You have already seen this style: `ConfigModule.forRoot({...})`. A dynamic module has a static method returning a `DynamicModule` object.

```ts
import { DynamicModule, Module } from '@nestjs/common';

export interface GreeterOptions {
  greeting: string;
}

@Module({})
export class GreeterModule {
  static forRoot(options: GreeterOptions): DynamicModule {
    return {
      module: GreeterModule,
      providers: [
        { provide: 'GREETER_OPTIONS', useValue: options },
        GreeterService,
      ],
      exports: [GreeterService],
    };
  }
}
```

```ts
import { Inject, Injectable } from '@nestjs/common';

@Injectable()
export class GreeterService {
  constructor(@Inject('GREETER_OPTIONS') private options: GreeterOptions) {}

  greet(name: string) {
    return `${this.options.greeting}, ${name}!`;
  }
}
```

Using it:

```ts
@Module({
  imports: [GreeterModule.forRoot({ greeting: 'Hello' })],
})
export class AppModule {}
```

Naming conventions used across the Nest ecosystem:

| Method | Meaning |
|---|---|
| `forRoot()` | Configure once for the whole app |
| `forRootAsync()` | Same, but options come from async work or other providers |
| `forFeature()` | Add feature-specific pieces (for example entities) |
| `register()` | Configure per importing module |

---

[Previous](./[2]-TypeScript-And-Decorators.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[4]-Controllers-And-Routing.md)