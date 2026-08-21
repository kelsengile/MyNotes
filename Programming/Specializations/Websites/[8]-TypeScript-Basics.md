[Previous](./[7]-Error-Handling-and-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[9]-CSS-Preprocessors.md)

*Intermediate JavaScript*

# Lesson 8 - TypeScript Basics

## 8.1 What TypeScript Adds

**TypeScript** is a superset of JavaScript that adds a **static type system**. Every valid JavaScript file is already valid TypeScript — you opt into typing incrementally. TypeScript code is compiled ("transpiled") down to plain JavaScript before it runs in a browser, since browsers don't understand TypeScript directly.

The main benefit: type errors (passing a string where a number is expected, misspelling a property name) are caught while you write code, in your editor, instead of at runtime in front of a user.

---

## 8.2 Basic Type Annotations

```ts
let age: number = 30;
let name: string = "Ada";
let isActive: boolean = true;

function greet(user: string): string {
  return `Hello, ${user}`;
}
```

The `: type` after a variable or parameter is an **annotation**. The `: string` after the function's parameters annotates its return type.

---

## 8.3 Interfaces and Object Shapes

An `interface` describes the shape an object must have:

```ts
interface User {
  id: number;
  name: string;
  email?: string; // optional property
}

function printUser(user: User) {
  console.log(user.name);
}
```

If you call `printUser({ id: 1 })`, TypeScript flags an error at compile time because `name` is missing — long before that bug would have shown up as `undefined` in production.

---

## 8.4 Type Inference

You don't need to annotate everything — TypeScript infers types from context where it can:

```ts
let count = 5; // inferred as number
count = "five"; // Error: Type 'string' is not assignable to type 'number'
```

Good practice is to annotate function parameters and return types explicitly, and let TypeScript infer the rest.

---

## 8.5 Union Types and Generics, Briefly

A **union type** allows a value to be one of several types:

```ts
function formatId(id: number | string): string {
  return `ID-${id}`;
}
```

**Generics** let a function or type work with any type while preserving that type's identity:

```ts
function firstItem<T>(items: T[]): T {
  return items[0];
}
```

You'll see both patterns increasingly as codebases grow, especially in framework and library code covered in later lessons.

---

[Previous](./[7]-Error-Handling-and-Debugging.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[9]-CSS-Preprocessors.md)
