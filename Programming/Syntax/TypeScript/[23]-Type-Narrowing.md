[Previous](./[22]-Union-and-Intersection-Types.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[24]-Discriminated-Unions.md)

*Union, Intersection & Narrowing*

# Lesson 23 - Type Narrowing (typeof, Instanceof, In)

## 23.1 What Narrowing Means

Narrowing is TypeScript's process of refining a broad type (like a union) down to a more specific one, based on runtime checks in your code. After a successful check, TypeScript "remembers" the narrower type within that branch.

```typescript
function printValue(value: string | number) {
  console.log(value.toUpperCase()); // Error — not valid for 'number'
}
```

Narrowing lets you safely resolve exactly this kind of error.

---

## 23.2 Narrowing with `typeof`

The `typeof` operator narrows between JavaScript's primitive types:

```typescript
function printValue(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase()); // value: string
  } else {
    console.log(value.toFixed(2));    // value: number
  }
}
```

`typeof` works for `"string"`, `"number"`, `"boolean"`, `"function"`, `"undefined"`, `"symbol"`, `"bigint"`, and `"object"`.

---

## 23.3 Narrowing with `instanceof`

`instanceof` narrows between class instances:

```typescript
class NetworkError extends Error {}
class ValidationError extends Error {}

function handle(error: NetworkError | ValidationError) {
  if (error instanceof NetworkError) {
    console.log("Retry the request"); // error: NetworkError
  } else {
    console.log("Show validation message"); // error: ValidationError
  }
}
```

---

## 23.4 Narrowing with `in`

The `in` operator checks whether a property exists on an object, which is especially useful for narrowing between object shapes that don't share a common discriminant property:

```typescript
type Fish = { swim: () => void };
type Bird = { fly: () => void };

function move(animal: Fish | Bird) {
  if ("swim" in animal) {
    animal.swim(); // animal: Fish
  } else {
    animal.fly();  // animal: Bird
  }
}
```

---

## 23.5 Truthiness and Equality Narrowing

Plain truthiness checks and equality comparisons also narrow types, without needing any special operator:

```typescript
function printName(name: string | null | undefined) {
  if (name) {
    console.log(name.toUpperCase()); // name: string (null/undefined excluded)
  }
}

function compare(a: string | number, b: string) {
  if (a === b) {
    console.log(a.toUpperCase()); // a narrowed to string, since it equals a string
  }
}
```

Together, `typeof`, `instanceof`, `in`, and truthiness/equality checks cover the vast majority of everyday narrowing needs — more structured patterns for object unions are covered next, in Lesson 24.

---

[Previous](./[22]-Union-and-Intersection-Types.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[24]-Discriminated-Unions.md)
