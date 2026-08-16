[Previous](./[9]-Type-Inference.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[11]-Enums.md)

*Core Types*

# Lesson 10 - Type Assertions And Type Casting

## 10.1 What a Type Assertion Is

A type assertion tells the compiler "trust me, I know this value's type better than you do" — it does **not** convert or check the value at runtime, it just changes how TypeScript treats it during compilation:

```typescript
let input: unknown = "42";
let length: number = (input as string).length;
```

If you're wrong about the actual type, a type assertion won't stop a runtime error — it only silences the compiler.

---

## 10.2 The `as` Syntax

The standard, widely-used syntax is `as`:

```typescript
let element = document.getElementById("app") as HTMLDivElement;
element.style.padding = "10px"; // now allowed, since HTMLDivElement has a .style property
```

Without the assertion, `document.getElementById` returns `HTMLElement | null`, which doesn't guarantee a `.style` of the right kind or that the element even exists.

---

## 10.3 The Angle-Bracket Syntax

An older, alternative syntax uses angle brackets:

```typescript
let element = <HTMLDivElement>document.getElementById("app");
```

This form is functionally identical to `as`, but is avoided in `.tsx` files (React) because it conflicts with JSX syntax. For that reason, `as` is the generally preferred style across most codebases.

---

## 10.4 The `as const` Assertion

`as const` is a special assertion that makes a value's type as narrow and immutable as possible:

```typescript
let color = "blue" as const;         // type: "blue", not string
let point = { x: 1, y: 2 } as const; // all properties become readonly, literal types
let dirs = ["up", "down"] as const;  // type: readonly ["up", "down"]
```

This is especially useful for values meant to be used as literal types elsewhere, such as keys in a lookup object or valid values for a union type.

---

## 10.5 The Double Assertion, and When to Avoid Assertions

TypeScript normally only allows an assertion between compatible types. To force an assertion between unrelated types, you can route through `unknown`:

```typescript
let value = "hello" as unknown as number; // allowed, but almost always a mistake
```

This is a strong signal to stop and reconsider — a double assertion through `unknown` bypasses type safety entirely. Type assertions should be reserved for cases where you genuinely have more information than the compiler (like DOM queries or narrowing `unknown`), not as a shortcut to silence legitimate type errors.

---

[Previous](./[9]-Type-Inference.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[11]-Enums.md)
