[Previous](./[7]-Objects-and-Type-Literals.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[9]-Type-Inference.md)

*Core Types*

# Lesson 8 - Any, Unknown, Void And Never

## 8.1 `any`: Opting Out of Type Checking

`any` tells TypeScript "don't check this value at all." You can assign anything to it, call anything on it, and pass it anywhere:

```typescript
let data: any = 42;
data = "now a string";
data.someRandomMethod(); // No error, even though this will crash at runtime
```

`any` is sometimes necessary — for example, wrapping loosely-typed third-party code — but it silently disables the safety TypeScript exists to provide. Overusing `any` is one of the most common ways TypeScript projects end up with the same bugs as plain JavaScript.

---

## 8.2 `unknown`: The Safe Alternative

`unknown` also accepts any value, but unlike `any`, TypeScript won't let you *use* it until you've narrowed it to a specific type:

```typescript
let input: unknown = "hello";

input.toUpperCase(); // Error: Object is of type 'unknown'.

if (typeof input === "string") {
  input.toUpperCase(); // OK — TypeScript now knows input is a string
}
```

`unknown` is the right choice for values whose type genuinely isn't known yet, such as data parsed from JSON or API responses — it forces you to check before acting, rather than trusting blindly. Type narrowing techniques for this are covered fully in Lesson 23.

---

## 8.3 `void`: No Meaningful Return Value

`void` describes a function that doesn't return a useful value — most commonly, functions run purely for their side effects:

```typescript
function logMessage(message: string): void {
  console.log(message);
}
```

A `void`-returning function may still technically `return;` with no value, but a returned value is not treated as meaningful and can't be used by the caller.

---

## 8.4 `never`: A Value That Can't Exist

`never` represents something that never actually produces a value — a function that always throws, or always loops forever:

```typescript
function throwError(message: string): never {
  throw new Error(message);
}

function infiniteLoop(): never {
  while (true) {}
}
```

`never` also appears as the result of exhaustive type narrowing — when every possible case of a union has already been handled, the remaining type is `never`. This makes it a powerful tool for catching missed cases at compile time, explored further in Lesson 24.

---

## 8.5 Choosing Between Them

| Type | Meaning | Safe to use freely? |
|---|---|---|
| `any` | Turns off type checking | No — avoid where possible |
| `unknown` | Value of an unknown type, must narrow before use | Yes |
| `void` | Function returns nothing meaningful | Yes |
| `never` | Value that logically cannot occur | Yes |

As a rule of thumb: reach for `unknown` instead of `any` whenever you're tempted to disable type checking — you get the same flexibility to accept anything, without giving up TypeScript's safety net.

---

[Previous](./[7]-Objects-and-Type-Literals.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[9]-Type-Inference.md)
