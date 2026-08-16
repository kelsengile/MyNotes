[Previous](./[21]-Index-Signatures.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[23]-Type-Narrowing.md)

*Union, Intersection & Narrowing*

# Lesson 22 - Union And Intersection Types

## 22.1 What a Union Type Is

A union, written with `|`, means a value can be **one of several** types:

```typescript
let id: string | number;

id = "abc123"; // OK
id = 42;       // OK
id = true;     // Error: Type 'boolean' is not assignable to type 'string | number'.
```

Unions are one of TypeScript's most-used features — they model real-world variability far more precisely than falling back to `any`.

---

## 22.2 Unions of Literal Types

Union members don't need to be full types — they can be specific literal values, creating a closed, checked set of allowed strings or numbers:

```typescript
type Status = "pending" | "active" | "completed";

let orderStatus: Status = "pending";
orderStatus = "cancelled"; // Error: not assignable to type 'Status'.
```

This pattern, introduced back in Lesson 11 as an enum alternative, is extremely common for representing a fixed set of states.

---

## 22.3 Working with Union Values Safely

Before using a member specific to only *some* branches of a union, TypeScript requires you to narrow it — checking which branch you actually have. This topic gets a full lesson to itself next (Lesson 23), but a quick preview:

```typescript
function printId(id: string | number) {
  if (typeof id === "string") {
    console.log(id.toUpperCase()); // safe — id is narrowed to string here
  } else {
    console.log(id.toFixed(2));    // safe — id is narrowed to number here
  }
}
```

---

## 22.4 What an Intersection Type Is

An intersection, written with `&`, combines multiple types into one that must satisfy **all** of them simultaneously:

```typescript
type Timestamped = { createdAt: Date };
type Named = { name: string };

type Event = Timestamped & Named;

const launch: Event = {
  name: "Launch",
  createdAt: new Date(),
}; // must satisfy both shapes
```

Where a union means "any one of these," an intersection means "every one of these, all at once."

---

## 22.5 Intersections with Overlapping Properties

If two intersected object types define the same property with incompatible types, the resulting property type becomes `never` — since no value could ever satisfy both simultaneously:

```typescript
type A = { value: string };
type B = { value: number };

type Combined = A & B; // Combined.value has type `never`

const impossible: Combined = { value: "x" }; // Error — no value can satisfy `never`
```

This is a useful signal that two types probably shouldn't be intersected as-is — usually a sign one of them should be redesigned, or a union (rather than an intersection) is what's actually intended.

---

[Previous](./[21]-Index-Signatures.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[23]-Type-Narrowing.md)
