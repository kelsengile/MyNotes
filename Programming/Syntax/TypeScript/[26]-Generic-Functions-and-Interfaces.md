[Previous](./[25]-Type-Guards.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[27]-Generic-Constraints.md)

*Generics*

# Lesson 26 - Generic Functions And Interfaces

## 26.1 The Problem Generics Solve

Without generics, writing a function that works with multiple types forces a choice between duplicating code per type, or falling back to `any` and losing type safety:

```typescript
function firstAny(arr: any[]): any {
  return arr[0];
}

const num = firstAny([1, 2, 3]);      // typed as `any` — no safety
const str = firstAny(["a", "b"]);     // also `any` — lost the fact it's a string
```

Generics let a function stay flexible across types *without* giving up type information.

---

## 26.2 Writing a Generic Function

A type parameter, conventionally named `T`, is declared in angle brackets before the parameter list, then used like any other type inside the function:

```typescript
function first<T>(arr: T[]): T {
  return arr[0];
}

const num = first([1, 2, 3]);    // T inferred as number → return type: number
const str = first(["a", "b"]);   // T inferred as string → return type: string
```

TypeScript infers `T` automatically from the argument passed in — you rarely need to specify it explicitly, though you can with `first<number>([1, 2, 3])` if inference alone isn't enough.

---

## 26.3 Multiple Type Parameters

A function can take more than one type parameter, letting relationships between several values be captured precisely:

```typescript
function pair<A, B>(first: A, second: B): [A, B] {
  return [first, second];
}

const result = pair("age", 29); // inferred as [string, number]
```

---

## 26.4 Generic Interfaces

Interfaces can also be parameterized over a type, just like the generic type aliases from Lesson 17:

```typescript
interface Box<T> {
  value: T;
}

const numberBox: Box<number> = { value: 42 };
const stringBox: Box<string> = { value: "hello" };
```

A generic interface is filled in with a concrete type wherever it's used — `Box<number>` and `Box<string>` are effectively two different, fully type-checked shapes generated from one definition.

---

## 26.5 A Practical Generic Interface: A Typed Cache

```typescript
interface Cache<T> {
  get(key: string): T | undefined;
  set(key: string, value: T): void;
}

class MemoryCache<T> implements Cache<T> {
  private store = new Map<string, T>();

  get(key: string): T | undefined {
    return this.store.get(key);
  }

  set(key: string, value: T): void {
    this.store.set(key, value);
  }
}

const userCache = new MemoryCache<{ name: string }>();
userCache.set("u1", { name: "Amy" });
```

This is the same idea explored further with classes in Lesson 28 — a single, reusable implementation that stays fully type-safe no matter what type it's used with.

---

[Previous](./[25]-Type-Guards.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[27]-Generic-Constraints.md)
