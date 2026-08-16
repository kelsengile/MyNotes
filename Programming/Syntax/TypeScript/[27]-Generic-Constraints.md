[Previous](./[26]-Generic-Functions-and-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[28]-Generic-Classes.md)

*Generics*

# Lesson 27 - Generic Constraints (extends)

## 27.1 The Problem: Unconstrained Generics Know Nothing

A bare type parameter `T` could be *anything* — TypeScript won't let you assume it has any particular property or method:

```typescript
function getLength<T>(value: T): number {
  return value.length; // Error: Property 'length' does not exist on type 'T'.
}
```

TypeScript is right to complain here: nothing guarantees the caller passes something with a `.length` property.

---

## 27.2 Constraining a Type Parameter

Adding `extends` after a type parameter restricts what it can be, and unlocks whatever members that constraint guarantees:

```typescript
interface HasLength {
  length: number;
}

function getLength<T extends HasLength>(value: T): number {
  return value.length; // OK — every T is guaranteed to have `length`
}

getLength("hello");        // OK — strings have length
getLength([1, 2, 3]);      // OK — arrays have length
getLength(42);              // Error: number doesn't satisfy HasLength
```

`T` can still be filled in by many different types — the constraint just guarantees a minimum shared shape.

---

## 27.3 Constraining with `keyof`

A very common constraint pattern ties one type parameter to the keys of another, guaranteeing a property actually exists on an object before accessing it:

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

const user = { name: "Amy", age: 29 };

getProperty(user, "name"); // OK — inferred as string
getProperty(user, "email"); // Error: Argument of type '"email"' is not assignable to parameter of type '"name" | "age"'.
```

This is significantly safer than a plain `string` for `key`, which would allow any property name — including ones that don't exist.

---

## 27.4 Default Type Parameters with Constraints

Constraints can be combined with default type parameters (covered fully in Lesson 29), giving a generic a sensible fallback while still enforcing a minimum shape:

```typescript
interface Identifiable {
  id: string;
}

function findById<T extends Identifiable = Identifiable>(items: T[], id: string): T | undefined {
  return items.find((item) => item.id === id);
}
```

---

## 27.5 Why Constraints Matter

Without constraints, generics tend to either fall back to unsafe casting, or force every caller to pre-check things TypeScript could have verified automatically. Constraints let you keep a function fully generic and reusable, while still guaranteeing the specific capabilities its implementation actually depends on — the same trade-off explored again for classes in Lesson 28.

---

[Previous](./[26]-Generic-Functions-and-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[28]-Generic-Classes.md)
