[Previous](./[4]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B6%5D-Numbers-Strings-and-Booleans%20%281%29.md)

*Core Syntax*

# Lesson 5 - Variables And Data Types

## 5.1 Declaring Variables: let, const, var

A **variable** is a named container for a value. JavaScript has three ways to declare one:

```js
let age = 25;       // can be reassigned later
const name = "Ava";  // cannot be reassigned
var city = "Cebu";   // old-style, avoid in new code
```

Use `const` by default — it signals the value won't be reassigned, which makes code easier to reason about. Use `let` only when you know the value needs to change. Avoid `var` in new code; it has confusing scoping rules that `let` and `const` were introduced to fix (see Lesson 10 for details).

```js
let score = 10;
score = 20; // fine, `let` allows reassignment

const pi = 3.14159;
pi = 3; // TypeError: Assignment to constant variable.
```

---

## 5.2 Naming Rules And Conventions

Variable names:
- Can contain letters, digits, `_`, and `$`.
- Cannot start with a digit.
- Are case-sensitive (`age` and `Age` are different variables).
- Cannot be a reserved word (`let`, `class`, `return`, etc.).

By convention, JavaScript uses **camelCase** for variables and functions (`firstName`, `totalPrice`), and reserves **PascalCase** for classes (`UserAccount`, covered in Lesson 18).

---

## 5.3 The Primitive Data Types

JavaScript has seven **primitive** (basic, unchangeable) data types:

| Type | Example |
|---|---|
| `number` | `42`, `3.14` |
| `string` | `"hello"` |
| `boolean` | `true`, `false` |
| `undefined` | a declared variable with no assigned value |
| `null` | an intentionally empty value |
| `bigint` | `9007199254740993n` — for numbers larger than `number` can safely hold |
| `symbol` | a unique, unforgeable identifier (covered in Lesson 31) |

```js
let a = 42;          // number
let b = "hello";     // string
let c = true;         // boolean
let d;                 // undefined
let e = null;         // null
```

Everything that isn't a primitive is an **object** — including arrays and functions — which Lessons 13, 14, and 18 cover in depth.

---

## 5.4 undefined vs. null

These two are easy to confuse:

- **`undefined`** means a variable exists but hasn't been given a value, or a function didn't explicitly return anything.
- **`null`** means "no value," set deliberately by you or your code.

```js
let x;
console.log(x); // undefined — never assigned

let y = null;
console.log(y); // null — intentionally empty
```

---

## 5.5 Checking A Type With typeof

The `typeof` operator returns a string naming a value's type — useful for debugging or validating input:

```js
typeof 42;          // "number"
typeof "hi";         // "string"
typeof true;         // "boolean"
typeof undefined;    // "undefined"
typeof null;         // "object"  (a long-standing quirk/bug in JavaScript)
typeof [1, 2, 3];    // "object"
typeof function(){}; // "function"
```

Note the famous quirk: `typeof null` returns `"object"`, not `"null"`. This is a mistake baked into JavaScript since 1995 that can never be fixed without breaking the web, so it's simply something to remember.

[Previous](./[4]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B6%5D-Numbers-Strings-and-Booleans%20%281%29.md)
