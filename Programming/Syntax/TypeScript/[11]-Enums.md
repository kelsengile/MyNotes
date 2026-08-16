[Previous](./[10]-Type-Assertions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[12]-Typing-Functions.md)

*Core Types*

# Lesson 11 - Enums (Numeric, String, Const)

## 11.1 What Enums Are For

An `enum` defines a named set of related constant values, useful when a variable should only ever hold one of a fixed set of options:

```typescript
enum Direction {
  Up,
  Down,
  Left,
  Right,
}

let move: Direction = Direction.Up;
```

Enums give these related constants a shared, readable name, instead of scattering raw string or number literals throughout your code.

---

## 11.2 Numeric Enums

By default, enum members are assigned increasing numbers starting at `0`:

```typescript
enum Status {
  Pending,   // 0
  Active,    // 1
  Completed, // 2
}
```

You can also set a custom starting value, and later members will increment from it:

```typescript
enum HttpStatus {
  OK = 200,
  NotFound = 404,
  ServerError = 500,
}
```

Numeric enums also support **reverse mapping** — you can look up the member name from its value: `Status[1]` evaluates to `"Active"`.

---

## 11.3 String Enums

String enums require every member to have an explicit string value, and don't support reverse mapping:

```typescript
enum LogLevel {
  Info = "INFO",
  Warning = "WARNING",
  Error = "ERROR",
}

console.log(LogLevel.Error); // "ERROR"
```

String enums are generally preferred over numeric enums in practice — their values are meaningful when logged or debugged, rather than an opaque number.

---

## 11.4 `const enum`

Adding `const` before `enum` tells the compiler to fully inline enum values at compile time, rather than generating a lookup object at runtime:

```typescript
const enum Direction {
  Up,
  Down,
}

let move = Direction.Up; // compiles directly to: let move = 0;
```

This produces smaller, faster output, but sacrifices some flexibility (like reverse mapping) and doesn't work with certain build tools that compile files in isolation (like `esbuild` or `ts-node`'s transpile-only mode). Check your toolchain's compatibility before relying on it.

---

## 11.5 An Alternative: Union of String Literals

Many modern TypeScript codebases avoid enums altogether in favor of a union of string literal types, which requires no runtime code at all:

```typescript
type Direction = "up" | "down" | "left" | "right";

let move: Direction = "up";
```

This achieves a very similar goal — a fixed, checked set of allowed values — without generating any JavaScript output. Union literal types are covered fully in Lesson 22; many style guides (see Lesson 80) recommend them over enums for this reason.

---

[Previous](./[10]-Type-Assertions.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[12]-Typing-Functions.md)
