[Previous](./[4]-Controllers-And-Routing.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[6]-Middleware.md)

*Basics*

# Lesson 5 - Providers And Dependency Injection

## 5.1 Services as providers

A **provider** is any class Nest can create and inject into other classes. The most common kind is a **service**, which holds business logic and data access so controllers stay thin.

```ts
import { Injectable } from '@nestjs/common';

export interface Cat {
  id: number;
  name: string;
}

@Injectable()
export class CatsService {
  private readonly cats: Cat[] = [];

  create(name: string): Cat {
    const cat = { id: this.cats.length + 1, name };
    this.cats.push(cat);
    return cat;
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

`@Injectable()` marks the class as manageable by Nest's DI container. Register it in a module's `providers` array:

```ts
@Module({
  controllers: [CatsController],
  providers: [CatsService],
})
export class CatsModule {}
```

Other things that are providers: repositories, factories, helpers, and anything else you register in `providers`.

---

## 5.2 Constructor injection

**Dependency injection** means a class receives the things it needs instead of creating them itself. In Nest you declare them as constructor parameters:

```ts
@Controller('cats')
export class CatsController {
  constructor(private readonly catsService: CatsService) {}

  @Get()
  findAll() {
    return this.catsService.findAll();
  }
}
```

What happens behind the scenes:

1. Nest reads the constructor's parameter types (Lesson 2.3).
2. It finds `CatsService` among the providers visible to this module.
3. It creates an instance (once) and passes it in.

Why this matters:

- **Testability:** in a test you can pass a fake `CatsService`.
- **Loose coupling:** the controller does not care how the service is built.
- **Reuse:** one instance is shared.

If Nest cannot resolve a dependency, it fails at startup with an error like `Nest can't resolve dependencies of the CatsController (?)`. The usual causes: the provider is missing from `providers`, or it lives in another module that was not imported (and exported).

**Circular dependencies** (A needs B and B needs A) can be handled with `forwardRef()`, but treat that as a sign to restructure:

```ts
constructor(
  @Inject(forwardRef(() => BService)) private readonly b: BService,
) {}
```

---

## 5.3 Provider scopes

A provider's **scope** controls its lifetime.

| Scope | Behavior | Use when |
|---|---|---|
| `DEFAULT` | One shared instance for the whole app | Almost always |
| `REQUEST` | A new instance for every incoming request | Per-request state (e.g. current tenant) |
| `TRANSIENT` | A new instance for every class that injects it | Each consumer needs its own copy |

```ts
import { Injectable, Scope } from '@nestjs/common';

@Injectable({ scope: Scope.REQUEST })
export class RequestContextService {}

@Injectable({ scope: Scope.TRANSIENT })
export class UniqueHelper {}
```

**Warning:** request scope **bubbles up**. A controller that depends on a request-scoped provider also becomes request-scoped, which creates objects on every request and costs performance. Prefer the default scope and pass per-request data as function arguments when possible.

---

## 5.4 Custom providers

Writing the class name in `providers` is shorthand for this longer form:

```ts
providers: [{ provide: CatsService, useClass: CatsService }]
```

The long form lets you control what is injected. A provider has a **token** (`provide`) and one way to produce the value:

**`useClass`**: swap implementations (great for tests or environments):

```ts
{
  provide: PaymentService,
  useClass: process.env.NODE_ENV === 'test' ? FakePaymentService : StripePaymentService,
}
```

**`useValue`**: inject a constant or mock:

```ts
{ provide: 'APP_NAME', useValue: 'My Nest App' }
```

**`useFactory`**: build the value with code, optionally using other providers:

```ts
{
  provide: 'API_CLIENT',
  useFactory: (config: ConfigService) => new ApiClient(config.get('API_URL')),
  inject: [ConfigService],
}
```

**`useExisting`**: create an alias to an existing provider:

```ts
{ provide: 'AliasedLogger', useExisting: LoggerService }
```

When the token is **not a class** (a string or symbol), inject it with `@Inject()`:

```ts
constructor(@Inject('APP_NAME') private readonly appName: string) {}
```

Mark a dependency as optional so a missing provider does not crash startup:

```ts
constructor(@Optional() @Inject('CACHE') private cache?: CacheService) {}
```

Async factories are allowed too; Nest waits for them before starting the app:

```ts
{
  provide: 'DB_CONNECTION',
  useFactory: async () => await createConnection(),
}
```

---

[Previous](./[4]-Controllers-And-Routing.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[6]-Middleware.md)