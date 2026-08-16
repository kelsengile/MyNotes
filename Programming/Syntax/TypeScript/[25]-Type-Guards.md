[Previous](./[24]-Discriminated-Unions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[26]-Generic-Functions-and-Interfaces.md)

*Union, Intersection & Narrowing*

# Lesson 25 - Type Guards And User-Defined Type Predicates

## 25.1 What a Type Guard Is

A **type guard** is any expression that TypeScript can use to narrow a type within a conditional branch. Every technique from Lesson 23 — `typeof`, `instanceof`, `in`, and equality checks — is a built-in type guard. This lesson covers how to write your **own**.

---

## 25.2 The Problem: Logic TypeScript Can't See Through

Sometimes a narrowing check is more complex than a single `typeof` or `in` can express, and needs to live inside its own reusable function:

```typescript
function isString(value: unknown) {
  return typeof value === "string";
}

function printLength(value: unknown) {
  if (isString(value)) {
    console.log(value.length); // Error — TypeScript doesn't know isString narrows anything
  }
}
```

Even though `isString` clearly checks for a string at runtime, TypeScript has no way to know that a plain `boolean` return value implies narrowing.

---

## 25.3 Writing a Type Predicate

A **type predicate** return type — `parameterName is Type` — tells TypeScript explicitly what a function's boolean result means for narrowing:

```typescript
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function printLength(value: unknown) {
  if (isString(value)) {
    console.log(value.length); // OK — value narrowed to string
  }
}
```

The function's actual runtime behavior is unchanged — the predicate only changes what TypeScript *understands* about the result.

---

## 25.4 Type Guards for Custom Object Shapes

This becomes especially useful for narrowing between related object shapes that don't have an obvious discriminant (see Lesson 24 for the discriminant-based alternative):

```typescript
interface Cat { meow: () => void; }
interface Dog { bark: () => void; }

function isCat(animal: Cat | Dog): animal is Cat {
  return "meow" in animal;
}

function speak(animal: Cat | Dog) {
  if (isCat(animal)) {
    animal.meow(); // narrowed to Cat
  } else {
    animal.bark();  // narrowed to Dog
  }
}
```

---

## 25.5 Array Filtering with Type Predicates

A common, practical use is filtering an array while also narrowing its element type, which a plain boolean-returning callback cannot do:

```typescript
const values: (string | null)[] = ["a", null, "b", null, "c"];

const strings: string[] = values.filter(
  (v): v is string => v !== null
);
```

Without the `v is string` predicate on the callback, `Array.prototype.filter` would still return `(string | null)[]`, even though every `null` has clearly been removed — the predicate is what lets TypeScript's type system keep up with what the code actually does.

---

[Previous](./[24]-Discriminated-Unions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[26]-Generic-Functions-and-Interfaces.md)
