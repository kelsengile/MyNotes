[Previous](./[1]-What-Is-NestJS.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[3]-Modules.md)

*Basics*

# Lesson 2 - TypeScript And Decorators

## 2.1 Classes and types

Nest code is mostly **classes**. TypeScript adds types to JavaScript classes.

```ts
interface Cat {
  name: string;
  age: number;
}

class CatsService {
  private cats: Cat[] = [];

  add(cat: Cat): void {
    this.cats.push(cat);
  }

  findAll(): Cat[] {
    return this.cats;
  }
}
```

Key ideas:

- **Types and interfaces** describe the shape of data. They exist only at compile time and are erased when the code becomes JavaScript.
- **Access modifiers** (`public`, `private`, `protected`) control who can use a member.
- **`readonly`** prevents reassignment.
- **Generics** (`Array<T>`, `Promise<T>`) let you write reusable, type-safe code.

TypeScript offers a shortcut called **parameter properties** that Nest code uses everywhere:

```ts
class CatsController {
  // Declares AND assigns this.catsService in one step
  constructor(private readonly catsService: CatsService) {}
}
```

This is equivalent to declaring a property and assigning it inside the constructor by hand.

---

## 2.2 How decorators work

A **decorator** is a special function that attaches extra behavior or information to a class, method, property, or parameter. It is written with `@` just above the thing it decorates.

```ts
// A class decorator: receives the class itself
function Logged(target: Function) {
  console.log(`Class created: ${target.name}`);
}

@Logged
class Example {}
```

There are four kinds, and Nest uses all of them:

| Kind | Example in Nest | Applied to |
|---|---|---|
| Class | `@Controller()`, `@Injectable()`, `@Module()` | A class |
| Method | `@Get()`, `@Post()`, `@UseGuards()` | A method |
| Property | `@Inject()`, `@IsString()` | A property |
| Parameter | `@Body()`, `@Param()`, `@Query()` | A method parameter |

A decorator that takes arguments is a function that returns a decorator (a "decorator factory"):

```ts
function Prefix(text: string) {
  return function (target: Function) {
    console.log(`${text}: ${target.name}`);
  };
}

@Prefix('Registered')
class Example {}
```

Nest uses the TypeScript "experimental" decorators, enabled in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

The Nest CLI sets both flags for you.

---

## 2.3 Metadata and reflection

Decorators mostly work by **storing metadata**: extra data attached to a class or method that can be read later. The `reflect-metadata` library provides this, and Nest imports it for you.

```ts
import 'reflect-metadata';

function Role(role: string) {
  return (target: any, key: string) => {
    Reflect.defineMetadata('role', role, target, key);
  };
}

class Demo {
  @Role('admin')
  secret() {}
}

const role = Reflect.getMetadata('role', Demo.prototype, 'secret');
console.log(role); // "admin"
```

When `emitDecoratorMetadata` is on, the TypeScript compiler also **automatically emits type information** for decorated classes. For a constructor, it records the parameter types under the key `design:paramtypes`:

```ts
@Injectable()
class CatsController {
  constructor(private catsService: CatsService) {}
}

const types = Reflect.getMetadata('design:paramtypes', CatsController);
console.log(types); // [ [class CatsService] ]
```

Because the class `CatsService` is a real runtime value, Nest can read this list and know exactly what to create and pass in.

---

## 2.4 Why Nest depends on them

Nest uses decorators and metadata for three jobs:

1. **Dependency injection.** Nest reads `design:paramtypes` to learn what each constructor needs, then supplies instances automatically.
2. **Routing.** `@Controller('cats')` and `@Get(':id')` store the route path and HTTP method as metadata. Nest scans for them at startup to build the route table.
3. **Cross-cutting features.** `@UseGuards()`, `@UsePipes()`, `@SetMetadata()`, and others attach behavior that Nest applies at the right moment.

Important consequences for you:

- **Interfaces and types do not exist at runtime**, so they cannot be used for injection or for validation. Use **classes** for DTOs and injectable tokens.
- A class must have **at least one decorator** (such as `@Injectable()`) for TypeScript to emit its constructor metadata.
- **Circular imports** between files can make a class `undefined` when metadata is read. Nest provides `forwardRef()` for this (see Lesson 5), but better design usually avoids the cycle.

```ts
// This works: CatsService is a class, so it exists at runtime
constructor(private readonly catsService: CatsService) {}

// This does NOT work for injection: an interface is erased at runtime
constructor(private readonly repo: CatRepositoryInterface) {}
```

---

[Previous](./[1]-What-Is-NestJS.md) | [Table of Contents](./[0]-Introduction-to-NextJS.md) | [Next](./[3]-Modules.md)