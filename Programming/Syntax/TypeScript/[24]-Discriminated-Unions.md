[Previous](./[23]-Type-Narrowing.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[25]-Type-Guards.md)

*Union, Intersection & Narrowing*

# Lesson 24 - Discriminated Unions

## 24.1 The Problem with Loosely Related Shapes

Narrowing with `in` (Lesson 23) works, but relies on checking for the presence of specific properties, which gets error-prone as shapes grow. A **discriminated union** solves this cleanly by giving every member of a union a shared, literal-typed property to switch on.

```typescript
type LoadingState = { status: "loading" };
type SuccessState = { status: "success"; data: string };
type ErrorState = { status: "error"; message: string };

type RequestState = LoadingState | SuccessState | ErrorState;
```

Here, `status` is the **discriminant** — a property present in every member, always a distinct literal value.

---

## 24.2 Narrowing on the Discriminant

Checking the discriminant property narrows the whole object, giving access to exactly the fields valid for that branch:

```typescript
function render(state: RequestState) {
  switch (state.status) {
    case "loading":
      return "Loading...";
    case "success":
      return state.data;      // safe — only SuccessState has `data`
    case "error":
      return state.message;   // safe — only ErrorState has `message`
  }
}
```

Inside each `case`, TypeScript automatically narrows `state` to just that one member of the union — no manual property checks needed.

---

## 24.3 Exhaustiveness Checking with `never`

Combining a discriminated union with a `default` case assigned to a variable typed `never` catches missing cases at **compile time**, not runtime:

```typescript
function render(state: RequestState) {
  switch (state.status) {
    case "loading":
      return "Loading...";
    case "success":
      return state.data;
    case "error":
      return state.message;
    default:
      const exhaustiveCheck: never = state;
      throw new Error(`Unhandled status: ${exhaustiveCheck}`);
  }
}
```

If a new member is later added to `RequestState` but its `case` is forgotten here, `state` in the `default` branch is no longer `never` — and TypeScript raises a compile error immediately, before the bug ever ships.

---

## 24.4 Discriminated Unions Beyond `switch`

The same narrowing works with `if`/`else if` chains, not just `switch`:

```typescript
function render(state: RequestState) {
  if (state.status === "loading") {
    return "Loading...";
  } else if (state.status === "success") {
    return state.data;
  } else {
    return state.message;
  }
}
```

`switch` tends to read more clearly once there are three or more branches, and pairs naturally with the exhaustiveness pattern from 24.3.

---

## 24.5 A Common Real-World Shape: Result Types

Discriminated unions are the foundation of type-safe error handling patterns, explored fully in Lesson 51:

```typescript
type Result<T> =
  | { success: true; value: T }
  | { success: false; error: string };

function divide(a: number, b: number): Result<number> {
  if (b === 0) {
    return { success: false, error: "Division by zero" };
  }
  return { success: true, value: a / b };
}
```

Callers are forced to check `success` before accessing `value` or `error`, making invalid states — like reading `value` on a failed result — impossible to represent.

---

[Previous](./[23]-Type-Narrowing.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[25]-Type-Guards.md)
