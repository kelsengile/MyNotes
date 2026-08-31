[Previous](./%5B5%5D-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B7%5D-Operators-and-Expressions.md)

*Core Syntax*

# Lesson 6 - Numbers, Strings And Booleans

## 6.1 Numbers

JavaScript has a single `number` type covering both integers and decimals (there's no separate "int" or "float"):

```js
let integer = 42;
let decimal = 3.14;
let negative = -7;
```

Numbers can misbehave slightly with decimals due to how computers store them in binary:

```js
0.1 + 0.2; // 0.30000000000000004
```

This isn't a JavaScript-specific bug — nearly every programming language does this. For money or anything requiring exact decimal precision, avoid raw floating-point math; Lesson 45 covers safer approaches.

Two special number values exist: `Infinity` (and `-Infinity`) and `NaN` ("Not a Number," the result of an invalid math operation like `0 / 0` or `"abc" * 2`).

```js
1 / 0;        // Infinity
"abc" * 2;    // NaN
```

---

## 6.2 Strings

A **string** is text, wrapped in single quotes, double quotes, or backticks:

```js
let single = 'Hello';
let double = "Hello";
let backtick = `Hello`;
```

Single and double quotes behave identically — pick one and stay consistent. Backticks create a **template literal**, which supports multi-line text and embedded expressions:

```js
let name = "Maria";
let greeting = `Hi, ${name}! You have ${2 + 3} new messages.`;
console.log(greeting); // "Hi, Maria! You have 5 new messages."
```

Lesson 11 covers template literals and string methods in depth.

---

## 6.3 Booleans

A **boolean** is one of exactly two values: `true` or `false`. Booleans usually come from comparisons:

```js
let isAdult = 20 >= 18;   // true
let isEmpty = "" === "";   // true
```

They control the flow of a program — Lesson 8 (conditionals) and Lesson 9 (loops) rely on booleans constantly.

---

## 6.4 Type Coercion

JavaScript will often convert values between types automatically — this is called **coercion**. It's convenient but a common source of bugs if you're not aware of it:

```js
"5" + 3;     // "53"  (number 3 becomes a string, then they're joined)
"5" - 3;     // 2     (string "5" becomes a number for subtraction)
"5" * "2";   // 10    (both strings become numbers)
true + 1;    // 2     (true becomes 1)
```

The rule of thumb: `+` with any string operand converts everything to strings and joins them; other arithmetic operators (`-`, `*`, `/`) convert operands to numbers.

---

## 6.5 Explicit Type Conversion

To avoid relying on automatic coercion, convert types explicitly:

```js
Number("42");     // 42
String(42);        // "42"
Boolean(0);         // false
Boolean("hello");  // true

// Common shorthand:
+"42";              // 42   (unary plus converts to number)
```

Values that convert to `false` when turned into a boolean are called **falsy**: `0`, `""`, `null`, `undefined`, `NaN`, and `false` itself. Every other value — including `"0"` and `[]` — is **truthy**. This concept becomes essential once you reach conditionals in Lesson 8.

[Previous](./%5B5%5D-Variables-and-Data-Types%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B7%5D-Operators-and-Expressions%20%281%29.md)
