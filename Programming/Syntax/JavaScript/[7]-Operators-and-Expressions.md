[Previous](./%5B6%5D-Numbers-Strings-and-Booleans.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B8%5D-Conditionals.md)

*Core Syntax*

# Lesson 7 - Operators And Expressions

## 7.1 Arithmetic Operators

```js
5 + 2;   // 7   addition
5 - 2;   // 3   subtraction
5 * 2;   // 10  multiplication
5 / 2;   // 2.5 division
5 % 2;   // 1   remainder (modulo)
5 ** 2;  // 25  exponentiation
```

Increment and decrement shorthand:

```js
let count = 0;
count++;  // count is now 1
count--;  // count is now 0
```

---

## 7.2 Assignment Operators

Beyond the basic `=`, JavaScript offers combined assignment operators that update a variable based on its current value:

```js
let total = 10;
total += 5;  // same as total = total + 5  →  15
total -= 3;  // 12
total *= 2;  // 24
total /= 4;  // 6
```

---

## 7.3 Comparison Operators

```js
5 == "5";   // true   (loose equality — allows coercion)
5 === "5";  // false  (strict equality — no coercion)
5 != "5";   // false
5 !== "5";  // true

5 > 3;      // true
5 < 3;      // false
5 >= 5;     // true
5 <= 4;     // false
```

**Always prefer `===` and `!==`** over `==` and `!=`. Loose equality's coercion rules produce surprising results (`0 == "0"` is `true`, but `0 == ""` is also `true`, while `"0" == ""` is `false`). Strict equality avoids this entire class of bug.

---

## 7.4 Logical Operators

```js
true && false;   // false  — AND: true only if both sides are true
true || false;   // true   — OR: true if either side is true
!true;             // false  — NOT: flips the boolean
```

Logical operators are commonly used to combine conditions:

```js
let age = 25;
let hasLicense = true;

if (age >= 18 && hasLicense) {
  console.log("Can drive");
}
```

`&&` and `||` don't just return `true`/`false` — they return one of the original operand values, which enables useful shortcuts:

```js
let username = "" || "Guest"; // "Guest" — falls back when the left side is falsy
```

---

## 7.5 Ternary Operator

A compact one-line alternative to a simple if/else, covered fully in Lesson 8:

```js
let age = 20;
let status = age >= 18 ? "adult" : "minor";
console.log(status); // "adult"
```

---

## 7.6 Nullish Coalescing And Optional Chaining

The **nullish coalescing operator** `??` provides a fallback, but only for `null` or `undefined` — unlike `||`, it doesn't trigger on other falsy values like `0` or `""`:

```js
let count = 0;
count || 10;  // 10  — 0 is falsy, so this overrides it (probably not what you want)
count ?? 10;  // 0   — 0 is neither null nor undefined, so it's kept
```

The **optional chaining operator** `?.` safely accesses a nested property, returning `undefined` instead of throwing an error if something along the chain doesn't exist:

```js
let user = { profile: { name: "Sam" } };

user.profile?.name;      // "Sam"
user.address?.city;      // undefined — no error, even though `address` doesn't exist
```

These two are often combined:

```js
let city = user.address?.city ?? "Unknown";
```

[Previous](./%5B6%5D-Numbers-Strings-and-Booleans%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./%5B8%5D-Conditionals%20%281%29.md)
