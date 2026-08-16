[Previous](./[28]-Generic-Classes.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[30]-Classes-and-Access-Modifiers.md)

*Generics*

# Lesson 29 - Default Generic Parameters

## 29.1 The Problem: Always Specifying a Type Gets Repetitive

Without a default, every use of a generic type must supply a type argument, even when one particular choice is by far the most common:

```typescript
interface ApiResponse<T> {
  data: T;
  error: string | null;
}

let response: ApiResponse<unknown>; // have to specify something, even generically
```

---

## 29.2 Giving a Type Parameter a Default

A default value is written with `=`, just like a default function parameter (Lesson 13), and is used whenever no type argument is explicitly provided:

```typescript
interface ApiResponse<T = unknown> {
  data: T;
  error: string | null;
}

let generic: ApiResponse;             // T defaults to unknown
let specific: ApiResponse<string>;    // T explicitly set to string
```

---

## 29.3 Defaults on Generic Functions and Classes

The same syntax applies to functions and classes:

```typescript
function createList<T = string>(): T[] {
  return [];
}

const list = createList(); // inferred as string[], using the default

class EventEmitter<T = void> {
  private listeners: ((payload: T) => void)[] = [];

  emit(payload: T) {
    this.listeners.forEach((listener) => listener(payload));
  }
}

const simpleEmitter = new EventEmitter(); // T defaults to void
```

---

## 29.4 Combining Defaults with Constraints

A type parameter can have both a constraint (Lesson 27) and a default at the same time — the constraint limits what's valid, and the default fills in when nothing is specified:

```typescript
interface Identifiable {
  id: string;
}

interface DefaultItem extends Identifiable {
  id: string;
  label: string;
}

class Collection<T extends Identifiable = DefaultItem> {
  items: T[] = [];
}

const defaultCollection = new Collection();          // T = DefaultItem
const userCollection = new Collection<User>();        // T = User, as long as User extends Identifiable
```

---

## 29.5 When to Reach for a Default

Defaults are most valuable on generic types that are used often with one "typical" case, but still need to stay flexible for less common ones — API response wrappers, event systems, and generic UI component props are all common examples. As a rule, only add a default when there's a genuinely sensible fallback; forcing an arbitrary default onto a generic that has no natural common case just hides missing type information instead of clarifying it.

---

[Previous](./[28]-Generic-Classes.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[30]-Classes-and-Access-Modifiers.md)
