[Previous](./[19]-Extending-Interfaces-and-Declaration-Merging.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[21]-Index-Signatures.md)

*Interfaces & Type Aliases*

# Lesson 20 - Readonly And Optional Properties

## 20.1 Optional Properties Recap

A property suffixed with `?` may be omitted when creating the object, and its type includes `undefined` wherever it's read:

```typescript
interface Draft {
  title: string;
  subtitle?: string;
}

const post: Draft = { title: "Hello World" }; // subtitle omitted — OK

console.log(post.subtitle.length); // Error: Object is possibly 'undefined'.
console.log(post.subtitle?.length); // OK — optional chaining
```

Optional chaining (`?.`) safely short-circuits to `undefined` instead of throwing if the property isn't there — one of the most common ways to work with optional data.

---

## 20.2 The `readonly` Modifier

A `readonly` property can be set when the object is first created, but never reassigned afterward:

```typescript
interface Point {
  readonly x: number;
  readonly y: number;
}

const origin: Point = { x: 0, y: 0 };
origin.x = 5; // Error: Cannot assign to 'x' because it is a read-only property.
```

This is a compile-time-only guarantee — like all TypeScript checks, `readonly` disappears once compiled to JavaScript, and doesn't provide runtime enforcement (for that, `Object.freeze` is the JavaScript-level equivalent).

---

## 20.3 `readonly` Only Prevents Reassignment, Not Deep Mutation

A `readonly` property can't be replaced, but if its value is itself a mutable object, that object's own properties can still change:

```typescript
interface Box {
  readonly items: string[];
}

const box: Box = { items: ["a", "b"] };
box.items = ["c"];     // Error — reassigning the property itself
box.items.push("c");   // OK — mutating the array's contents is allowed
```

To lock down the contents as well, combine it with `readonly` arrays/tuples (Lesson 6), or a deep-readonly utility type built with mapped types (Lesson 35).

---

## 20.4 Combining Optional and Readonly

Both modifiers can be applied to the same property, in either order:

```typescript
interface Settings {
  readonly id: string;
  theme?: "light" | "dark";
}
```

`id` must always be present and can never be reassigned; `theme` may be entirely absent, but can be freely changed if it exists.

---

## 20.5 Deriving Readonly/Partial Versions of a Type

Rather than writing separate variants by hand, TypeScript's built-in utility types can transform an existing shape automatically:

```typescript
interface User {
  id: string;
  name: string;
}

type ReadonlyUser = Readonly<User>; // every property becomes readonly
type PartialUser = Partial<User>;   // every property becomes optional
```

These utilities (fully explored in Lesson 39) are built using mapped types (Lesson 35) — understanding `readonly` and `?` at the property level is exactly what makes those more advanced tools make sense later.

---

[Previous](./[19]-Extending-Interfaces-and-Declaration-Merging.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[21]-Index-Signatures.md)
