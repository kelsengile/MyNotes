[Previous](./[16]-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[18]-Interfaces-vs-Type-Aliases.md)

*Interfaces & Type Aliases*

# Lesson 17 - Type Aliases

## 17.1 What a Type Alias Is

A `type` alias gives a name to *any* type — not just object shapes — using an `=` assignment:

```typescript
type UserId = number;
type Username = string;

let id: UserId = 1;
let name: Username = "amy";
```

This is useful even for primitives: naming `UserId` separately from a plain `number` documents intent, even though the underlying type is identical.

---

## 17.2 Aliasing Object Shapes

Type aliases can describe object shapes, just like interfaces:

```typescript
type User = {
  id: number;
  name: string;
  email: string;
};
```

Structurally, this behaves the same as the equivalent `interface User { ... }` from Lesson 16 — any object with matching properties satisfies the type.

---

## 17.3 Aliasing Unions, Tuples, and Functions

Unlike interfaces, type aliases can name *any* kind of type — this is where they really distinguish themselves:

```typescript
type Status = "pending" | "active" | "completed"; // union of literals
type Point = [number, number];                    // tuple
type Comparator = (a: number, b: number) => number; // function type
```

None of these can be expressed as an `interface`, since interfaces are limited to describing object and function shapes.

---

## 17.4 Combining Types with `&`

Type aliases can combine other types using the intersection operator, `&`, merging multiple shapes into one:

```typescript
type Timestamped = { createdAt: Date };
type Named = { name: string };

type Event = Timestamped & Named;

const launch: Event = { name: "Launch", createdAt: new Date() };
```

Intersections are covered fully in Lesson 22, alongside unions.

---

## 17.5 Generic Type Aliases

Like functions, type aliases can be generic — parameterized over another type, filled in at the point of use:

```typescript
type ApiResponse<T> = {
  data: T;
  error: string | null;
};

let userResponse: ApiResponse<User> = {
  data: { id: 1, name: "Amy", email: "amy@example.com" },
  error: null,
};
```

This pattern is extremely common for describing reusable shapes — like API responses or wrapper types — whose contents vary by use case. Generics are covered in depth starting in Lesson 26.

---

[Previous](./[16]-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[18]-Interfaces-vs-Type-Aliases.md)
