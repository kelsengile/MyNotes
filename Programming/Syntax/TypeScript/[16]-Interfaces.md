[Previous](./[15]-This-Parameters-and-Typing-Callbacks.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[17]-Type-Aliases.md)

*Interfaces & Type Aliases*

# Lesson 16 - Interfaces

## 16.1 Giving a Shape a Name

An `interface` declares a reusable, named object shape, solving the problem raised in Lesson 7 of inline object types becoming unwieldy:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

function printUser(user: User) {
  console.log(`${user.name} (${user.email})`);
}
```

Any object matching this shape can be used where `User` is expected — TypeScript checks structure, not the name of the interface, a concept known as **structural typing** (also called "duck typing").

---

## 16.2 Optional and Readonly Members

Interfaces support the same modifiers as object type literals:

```typescript
interface Product {
  readonly id: string;
  name: string;
  discount?: number;
}
```

`readonly` prevents reassignment after the object is created; `?` marks a property as optional. Both are covered in more depth in Lesson 20.

---

## 16.3 Method Signatures

Interfaces can describe functions that belong to an object, either using method shorthand or a function-typed property:

```typescript
interface Logger {
  log(message: string): void;          // method shorthand
  warn: (message: string) => void;     // function-typed property
}
```

These two forms behave almost identically; method shorthand is slightly more permissive about parameter variance and is the more common style for object methods.

---

## 16.4 Interfaces Describing Function Types

Beyond object shapes, an interface can describe a callable function directly, using a call signature:

```typescript
interface Compare {
  (a: number, b: number): number;
}

const ascending: Compare = (a, b) => a - b;
```

This is functionally similar to a function type alias (`type Compare = (a: number, b: number) => number;`), covered in Lesson 17.

---

## 16.5 Implementing an Interface in a Class

Classes can formally declare that they satisfy an interface using `implements`, which TypeScript then enforces:

```typescript
interface Shape {
  area(): number;
}

class Circle implements Shape {
  constructor(private radius: number) {}

  area(): number {
    return Math.PI * this.radius ** 2;
  }
}
```

If `Circle` were missing the `area` method, or it had the wrong signature, TypeScript would report an error at the class declaration. This pattern is explored in full in Lesson 32.

---

[Previous](./[15]-This-Parameters-and-Typing-Callbacks.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[17]-Type-Aliases.md)
