[Previous](./[6]-Arrays-and-Tuples.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[8]-Any-Unknown-Void-and-Never.md)

*Core Types*

# Lesson 7 - Objects And Type Literals

## 7.1 Typing an Object Inline

You can describe the shape of an object directly, without a separate name, using an **object type literal**:

```typescript
let user: { name: string; age: number } = {
  name: "Amy",
  age: 29,
};
```

Every property listed must be present, with a matching type, or TypeScript reports an error. This inline form is called a type literal because the type itself looks like the shape of the object it describes.

---

## 7.2 Optional Properties

A property marked with `?` may be omitted entirely:

```typescript
let profile: { name: string; bio?: string } = {
  name: "Amy",
}; // OK — bio is optional
```

Accessing an optional property gives you `string | undefined`, so TypeScript will require you to check for its presence before treating it as a plain `string` (see Lesson 20 for more on optional properties, and Lesson 23 for narrowing).

---

## 7.3 Nested Object Shapes

Object type literals can nest arbitrarily deep, mirroring the structure of real-world data:

```typescript
let order: {
  id: number;
  customer: {
    name: string;
    email: string;
  };
  items: { sku: string; quantity: number }[];
} = {
  id: 1,
  customer: { name: "Amy", email: "amy@example.com" },
  items: [{ sku: "A1", quantity: 2 }],
};
```

In practice, deeply nested inline literals like this get unwieldy fast — which is exactly why `interface` (Lesson 16) and `type` aliases (Lesson 17) exist: to give a shape like this a reusable name.

---

## 7.4 Excess Property Checks

When assigning an **object literal** directly to a typed variable, TypeScript performs an extra, stricter check: it flags properties that aren't part of the declared type at all.

```typescript
let point: { x: number; y: number } = { x: 1, y: 2, z: 3 };
// Error: Object literal may only specify known properties, and 'z' does not exist in type '{ x: number; y: number }'.
```

This check only applies to object literals written directly in the assignment — assigning via an intermediate variable bypasses it, since TypeScript can no longer be sure the extra property is a mistake rather than intentional.

---

## 7.5 Object Types vs. `object`

Don't confuse a specific object type literal with the lowercase `object` type, which just means "any non-primitive value" and tells TypeScript almost nothing about its shape:

```typescript
let anything: object = { anything: "goes" };
anything.anything; // Error: Property 'anything' does not exist on type 'object'.
```

In nearly all real code, a precise shape (via a type literal, interface, or type alias) is far more useful than the bare `object` type.

---

[Previous](./[6]-Arrays-and-Tuples.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[8]-Any-Unknown-Void-and-Never.md)
