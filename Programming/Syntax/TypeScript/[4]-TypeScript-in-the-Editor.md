[Previous](./[3]-tsconfig-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[5]-Basic-Types.md)

*Getting Started*

# Lesson 4 - TypeScript In The Editor (IntelliSense, Type Checking)

## 4.1 Why the Editor Experience Matters

A huge part of TypeScript's value doesn't come from the command line — it comes from what your editor can tell you *while you type*. Real-time type checking, autocomplete, and inline documentation catch mistakes long before you ever run `tsc`.

---

## 4.2 Setting Up VS Code

Visual Studio Code ships with TypeScript support built in, powered by the same language service that drives `tsc`. No extension is required for basic support, but a few things are worth configuring:

- Make sure VS Code uses your project's local TypeScript version (not a bundled one) via the command palette: **"TypeScript: Select TypeScript Version" → "Use Workspace Version"**.
- This ensures editor errors match exactly what `tsc` would report, since they use the same compiler.

---

## 4.3 IntelliSense: Autocomplete and Signatures

IntelliSense uses your types to suggest properties, methods, and function signatures as you type:

```typescript
interface User {
  id: number;
  name: string;
  email: string;
}

function greetUser(user: User) {
  user. // <-- editor suggests: id, name, email
}
```

Hovering over a variable, function, or parameter also shows its inferred or declared type without needing to search documentation.

---

## 4.4 Inline Type Errors

As you type, the editor underlines problems in red, the same errors `tsc` would produce on the command line:

```typescript
function add(a: number, b: number): number {
  return a + b;
}

add(5, "10"); // Editor flags: Argument of type 'string' is not assignable to parameter of type 'number'.
```

This tight feedback loop is one of the biggest productivity gains TypeScript offers over plain JavaScript — bugs are caught while writing, not while running.

---

## 4.5 Quick Fixes and Refactoring Tools

Editors built on the TypeScript language service also offer automated actions:

- **Quick Fix** — auto-import a missing type, add a missing property, or fill in function stubs.
- **Rename Symbol** — safely rename a variable, function, or type across an entire project.
- **Go to Definition / Find All References** — jump straight to where something is declared or used.

These tools rely entirely on your types being accurate, which is one more reason to write precise type annotations as you go, rather than reaching for `any` out of convenience (see Lesson 8).

---

[Previous](./[3]-tsconfig-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[5]-Basic-Types.md)
