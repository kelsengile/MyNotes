[Previous](./[11]-Enums.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[13]-Optional-Default-and-Rest-Parameters.md)

*Functions*

# Lesson 12 - Typing Functions: Parameters And Return Types

## 12.1 Typing Parameters

Function parameters are annotated the same way as variables, with a colon after the name:

```typescript
function add(a: number, b: number) {
  return a + b;
}
```

Unlike variables, TypeScript **cannot** infer parameter types from thin air — every parameter should have an explicit type (or come from context, as covered in Lesson 9), or it implicitly becomes `any` and loses type safety.

---

## 12.2 Typing the Return Value

A return type can be written after the parameter list:

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

If the function body returns something inconsistent with the annotation, TypeScript flags it immediately:

```typescript
function add(a: number, b: number): number {
  return `${a}${b}`; // Error: Type 'string' is not assignable to type 'number'.
}
```

As discussed in Lesson 9, TypeScript can infer the return type automatically — but writing it explicitly on exported/public functions is good practice, since it locks in the intended contract.

---

## 12.3 Arrow Functions

Arrow functions are typed the same way, just with a different syntax:

```typescript
const multiply = (a: number, b: number): number => a * b;
```

Arrow functions assigned to a variable can also get their types from a separately-declared function type (see 12.4 below), which is common when passing functions as arguments or storing them in objects.

---

## 12.4 Function Types

You can describe the *shape* of a function — independent of any specific implementation — using a function type:

```typescript
let operation: (a: number, b: number) => number;

operation = (a, b) => a + b; // OK — parameter types inferred from context
operation = (a, b) => `${a}`; // Error: return type doesn't match
```

This is especially useful for typing parameters that accept a callback, or fields on an object that hold a function:

```typescript
interface Calculator {
  operate: (a: number, b: number) => number;
}
```

---

## 12.5 Void Functions and Unused Return Values

A function typed to return `void` can still be assigned an implementation that returns *something* — TypeScript simply ignores the extra return value. This flexibility exists specifically to support common callback patterns:

```typescript
let onClick: () => void;

onClick = () => {
  return true; // allowed, but the `true` is discarded by callers
};
```

This lets you pass functions like `array.push`, which technically return a number, anywhere a `void`-returning callback is expected.

---

[Previous](./[11]-Enums.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[13]-Optional-Default-and-Rest-Parameters.md)
