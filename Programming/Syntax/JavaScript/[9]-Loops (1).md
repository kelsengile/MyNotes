[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops

## 9.1 The for Loop

A `for` loop repeats code a specific number of times, controlled by three parts: an initializer, a condition, and an update step.

```js
for (let i = 0; i < 5; i++) {
  console.log(i);
}
// prints 0, 1, 2, 3, 4
```

- `let i = 0` — runs once, before the loop starts.
- `i < 5` — checked before every iteration; the loop stops once this is `false`.
- `i++` — runs after every iteration.

---

## 9.2 The while And do-while Loops

A `while` loop repeats as long as its condition stays true, checking the condition *before* each iteration:

```js
let count = 0;
while (count < 3) {
  console.log(count);
  count++;
}
// prints 0, 1, 2
```

A `do-while` loop checks the condition *after* each iteration, guaranteeing the body runs at least once:

```js
let attempts = 0;
do {
  console.log("Attempt", attempts);
  attempts++;
} while (attempts < 3);
```

Use `while` when you don't know the number of iterations in advance — for example, reading data until none remains.

---

## 9.3 for...of — Looping Over Values

`for...of` iterates over the **values** of an iterable — arrays, strings, and other collections covered later (Lessons 13 and 16):

```js
const fruits = ["apple", "banana", "cherry"];

for (const fruit of fruits) {
  console.log(fruit);
}
// apple, banana, cherry
```

This is the most common way to loop over an array when you need each item's value.

---

## 9.4 for...in — Looping Over Keys

`for...in` iterates over the **enumerable property names (keys)** of an object:

```js
const user = { name: "Lea", age: 30 };

for (const key in user) {
  console.log(key, user[key]);
}
// name Lea
// age 30
```

Avoid `for...in` on arrays — it iterates over index *keys* as strings and can include unexpected inherited properties. Use `for...of` (or array methods from Lesson 13) for arrays instead.

---

## 9.5 break And continue

`break` exits a loop immediately; `continue` skips to the next iteration without exiting:

```js
for (let i = 0; i < 10; i++) {
  if (i === 5) break;
  console.log(i);
}
// 0, 1, 2, 3, 4

for (let i = 0; i < 5; i++) {
  if (i % 2 === 0) continue; // skip even numbers
  console.log(i);
}
// 1, 3
```

---

## 9.6 Nested Loops

Loops can contain other loops — common when working with grids or combinations:

```js
for (let row = 1; row <= 2; row++) {
  for (let col = 1; col <= 3; col++) {
    console.log(`Row ${row}, Col ${col}`);
  }
}
```

Be mindful of performance: a loop nested inside another runs its inner body once for *every* iteration of the outer loop, so two loops of size 100 result in 10,000 total iterations.

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[10]-Functions-and-Scope.md)
