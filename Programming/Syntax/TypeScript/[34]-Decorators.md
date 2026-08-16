[Previous](./[33]-Parameter-Properties.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[35]-Mapped-Types.md)

*Object-Oriented TypeScript*

# Lesson 34 - Decorators

## 34.1 What Are Decorators

A decorator is a special kind of declaration that can be attached to a class, method, property, or parameter to observe, modify, or extend its behavior. Decorators use the `@expression` syntax, placed directly above the thing they decorate:

```ts
@sealed
class Greeter {
  @log
  greet(name: string): string {
    return `Hello, ${name}`;
  }
}
```

Under the hood, a decorator is just a function that receives information about the thing it's attached to and can return a modified version of it.

---

## 34.2 Enabling Decorators

TypeScript's decorator support has evolved over time. As of TypeScript 5.0, decorators follow the current ECMAScript proposal and work out of the box with no compiler flag required. Some codebases still use the older "experimental" decorators (common with frameworks like NestJS — see [Lesson 63](./[63]-NestJS.md)), which require enabling `experimentalDecorators` in `tsconfig.json`:

```json
{
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true
  }
}
```

Check your framework's documentation to see which style it expects, since the two are not fully interchangeable.

---

## 34.3 Class Decorators

A class decorator receives the class itself and can inspect or replace it:

```ts
function sealed(target: Function) {
  Object.seal(target);
  Object.seal(target.prototype);
}

@sealed
class Greeter {
  greeting: string = "Hello";
}
```

Here, `sealed` prevents new properties from being added to the class or its prototype after it's defined.

---

## 34.4 Method and Property Decorators

Method decorators can wrap a method to add behavior before or after it runs, which is useful for cross-cutting concerns like logging or timing:

```ts
function log(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;

  descriptor.value = function (...args: unknown[]) {
    console.log(`Calling ${propertyKey} with`, args);
    return original.apply(this, args);
  };

  return descriptor;
}

class Greeter {
  @log
  greet(name: string): string {
    return `Hello, ${name}`;
  }
}

new Greeter().greet("Sam");
// Logs: "Calling greet with [ 'Sam' ]"
// Returns: "Hello, Sam"
```

Property decorators follow a similar pattern but receive the target and property key, without a method descriptor, since properties don't have one.

---

## 34.5 Practical Use Cases

You'll rarely write low-level decorators like the ones above by hand in application code. Instead, you'll typically use decorators provided by a framework:

- **NestJS** uses decorators like `@Controller()` and `@Injectable()` to wire up dependency injection ([Lesson 63](./[63]-NestJS.md)).
- **TypeORM** uses decorators like `@Entity()` and `@Column()` to map classes to database tables ([Lesson 69](./[69]-TypeORM-and-Sequelize.md)).
- **Angular** uses decorators like `@Component()` to define UI components ([Lesson 59](./[59]-Angular-and-TypeScript.md)).

Understanding how decorators work under the hood makes it much easier to read and debug framework code that relies on them.

[Previous](./[33]-Parameter-Properties.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[35]-Mapped-Types.md)
