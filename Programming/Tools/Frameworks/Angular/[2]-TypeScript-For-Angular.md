[Previous](./[1]-What-Is-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[3]-Components-And-Templates.md)

*Basics*

# Lesson 2 - TypeScript For Angular

Angular code is TypeScript. You do not need to be a TypeScript expert, but you do need to read types, classes, decorators, and generics comfortably. This lesson covers exactly the TypeScript you will meet in Angular code.

---

## 2.1 Types and interfaces

TypeScript adds **static types** to JavaScript. The compiler checks them before your code runs, catching typos and wrong-shape data early.

```ts
let title: string = 'Angular';
let count: number = 3;
let isReady: boolean = true;
let tags: string[] = ['web', 'spa'];
let maybe: string | null = null;   // union type: string OR null
let anything: unknown;              // safe "don't know yet" type
```

TypeScript can often **infer** a type, so `let count = 3` is already a `number`. Avoid `any`, which switches type checking off; prefer `unknown` and narrow it.

An **interface** (or **type alias**) describes the shape of an object. In Angular you use them constantly to describe data from a server:

```ts
interface User {
  id: number;
  name: string;
  email?: string;        // optional property
  readonly role: 'admin' | 'member';   // literal union, cannot be reassigned
}

type UserId = User['id'];   // type alias derived from another type

const ada: User = { id: 1, name: 'Ada', role: 'admin' };
```

Useful built-in helpers:

```ts
type UserDraft = Partial<User>;               // every property optional
type UserPreview = Pick<User, 'id' | 'name'>; // only these properties
type UserNoRole = Omit<User, 'role'>;         // everything except role
```

Other operators you will see in templates and code:

```ts
const city = user?.address?.city;   // optional chaining: stops if undefined
const label = name ?? 'Guest';      // nullish coalescing: default for null/undefined
const el = maybeEl!;                // non-null assertion: "trust me" (use sparingly)
```

Angular projects enable `strict` mode, which makes the compiler check for possible `null`/`undefined` values. This is a feature: it prevents a whole class of runtime errors.

---

## 2.2 Classes and access modifiers

A **class** bundles data (properties) and behavior (methods). Components and services in Angular are classes.

```ts
class Counter {
  count = 0;                         // property with a default value

  increment(): void {                // method with a return type
    this.count++;
  }
}
```

**Access modifiers** control who can use a member:

| Modifier | Accessible from |
|---|---|
| `public` (default) | Anywhere |
| `protected` | The class and its subclasses |
| `private` | Only inside the class |
| `readonly` | Can be assigned once (declaration or constructor) |

```ts
class BankAccount {
  private balance = 0;
  readonly owner: string;

  constructor(owner: string) {
    this.owner = owner;
  }

  deposit(amount: number): void {
    this.balance += amount;
  }
}
```

TypeScript offers a shorthand called **parameter properties**: adding a modifier to a constructor parameter declares and assigns the property in one step.

```ts
class BankAccount {
  constructor(public readonly owner: string, private balance = 0) {}
}
```

In Angular, the template can only read `public` and `protected` members of a component. A `private` member is hidden from the template, so you will often mark helper state `private` and expose only what the template needs. Classes can also **implement interfaces** (`class X implements OnInit`), a pattern Angular uses for lifecycle hooks (Lesson 9).

---

## 2.3 Decorators

A **decorator** is a function written as `@Name(...)` placed just above a class, property, or parameter. It attaches extra information to the thing below it. Angular reads that information to know what your class *is*.

```ts
import { Component, Injectable } from '@angular/core';

@Component({
  selector: 'app-hello',
  template: '<h1>Hello</h1>',
})
export class HelloComponent {}      // "this class is a component"

@Injectable({ providedIn: 'root' })
export class LoggerService {}       // "this class can be injected"
```

Common Angular decorators:

| Decorator | Marks a class as... |
|---|---|
| `@Component` | A component with a template |
| `@Directive` | A directive (behavior without its own template) |
| `@Pipe` | A pipe that transforms values in templates |
| `@Injectable` | A service that can take part in dependency injection |
| `@NgModule` | A module (legacy; see Lesson 8) |

Older code also used property decorators such as `@Input()`, `@Output()`, `@ViewChild()`, and `@HostListener()`. Modern Angular replaces many of these with functions (`input()`, `output()`, `viewChild()`), which you will learn in Lessons 5 and 6. You must still recognize the decorator forms when reading older code.

A decorator is *not* a comment. Removing `@Component` from a class changes how Angular treats it.

---

## 2.4 Generics and modules

**Generics** let one piece of code work with many types while staying type-safe. The type is a placeholder written in angle brackets.

```ts
interface ApiResponse<T> {
  data: T;
  total: number;
}

const users: ApiResponse<User[]> = { data: [ada], total: 1 };

function first<T>(items: T[]): T | undefined {
  return items[0];
}

const firstName = first(['Ada', 'Linus']);   // inferred as string | undefined
```

You will see generics all over Angular: `signal<number>(0)`, `inject<T>()`, `http.get<User[]>(url)`, `Observable<User>`, `FormControl<string>`. When you read `Something<Type>`, think "a Something that deals with Type."

**ES modules** split code into files. Whatever you `export` from one file you can `import` in another:

```ts
// user.model.ts
export interface User { id: number; name: string; }

// user-list.component.ts
import { User } from './user.model';
```

Rules to remember:

- Paths starting with `./` or `../` are your own files; bare names such as `'@angular/core'` come from `node_modules`.
- A file with a top-level `import` or `export` is a module; its variables are private unless exported.
- Do not confuse ES modules (files) with Angular `NgModule` (an Angular-specific grouping, Lesson 8). They are unrelated concepts that share a word.

---

**Further reading:** [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/intro.html)

[Previous](./[1]-What-Is-Angular.md) | [Table of Contents](./[0]-Introduction-to-Angular.md) | [Next](./[3]-Components-And-Templates.md)
