[Previous](./[13]-Optional-Default-and-Rest-Parameters.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[15]-This-Parameters-and-Typing-Callbacks.md)

*Functions*

# Lesson 14 - Function Overloads

## 14.1 The Problem Overloads Solve

Sometimes a single function needs to behave differently depending on the *combination* of argument types it receives — more precisely than a simple union type can express. Function overloads let you declare multiple valid call signatures for one function.

```typescript
function makeDate(timestamp: number): Date;
function makeDate(month: number, day: number, year: number): Date;
function makeDate(monthOrTimestamp: number, day?: number, year?: number): Date {
  if (day !== undefined && year !== undefined) {
    return new Date(year, monthOrTimestamp, day);
  }
  return new Date(monthOrTimestamp);
}
```

---

## 14.2 Overload Signatures vs. the Implementation Signature

Notice the shape above: two **overload signatures** (with no body) followed by one **implementation signature** (with the actual body). Callers only ever see the overload signatures — the implementation signature is not directly callable from outside:

```typescript
makeDate(1706000000000);      // OK — matches first overload
makeDate(1, 15, 2024);        // OK — matches second overload
makeDate(1, 15);              // Error: No overload matches this call.
```

The implementation signature must be general enough (using optional parameters or unions) to handle every case described by the overloads above it.

---

## 14.3 Why Not Just Use a Union?

A union-typed parameter can't express relationships *between* parameters. Compare:

```typescript
// Union approach — loses the relationship between arguments
function makeDate(a: number, b?: number, c?: number): Date { /* ... */ }
makeDate(1, 15); // No compile error, but this is a nonsensical call at runtime
```

The overloaded version from 14.1 correctly rejects `makeDate(1, 15)`, because no overload signature matches "exactly two numbers." Overloads let you be precise about *which combinations* of arguments are actually valid.

---

## 14.4 Ordering Matters

TypeScript checks overload signatures top-to-bottom and uses the **first** one that matches. Put more specific overloads before more general ones:

```typescript
function process(value: string): string;
function process(value: string | number): string;
function process(value: string | number): string {
  return String(value);
}
```

If the broader `string | number` overload were listed first, calls with a plain `string` would match it before ever reaching the more specific `string`-only overload, defeating the purpose of having both.

---

## 14.5 A Practical Example

Overloads are common in library APIs where a function's return type depends on its input, such as a selector that returns either a single element or a list:

```typescript
function query(selector: string): Element | null;
function query(selector: string, all: true): NodeListOf<Element>;
function query(selector: string, all?: true) {
  return all
    ? document.querySelectorAll(selector)
    : document.querySelector(selector);
}

const single = query(".item");        // typed as Element | null
const many = query(".item", true);    // typed as NodeListOf<Element>
```

---

[Previous](./[13]-Optional-Default-and-Rest-Parameters.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[15]-This-Parameters-and-Typing-Callbacks.md)
