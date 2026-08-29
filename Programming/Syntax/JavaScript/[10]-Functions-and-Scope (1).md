[Previous](./%5B9%5D-Loops%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Functions And Scope

## 10.1 Function Declarations

A **function** packages reusable code so it can be run multiple times with different inputs:

```js
function greet(name) {
  return `Hello, ${name}!`;
}

console.log(greet("Ana")); // "Hello, Ana!"
console.log(greet("Rico")); // "Hello, Rico!"
```

`name` here is a **parameter** — a placeholder for the value (the **argument**) passed in when the function is called. `return` sends a value back to wherever the function was called; a function without `return` implicitly returns `undefined`.

---

## 10.2 Function Expressions And Arrow Functions

A function can also be stored in a variable, either as a regular function expression or the more compact **arrow function** syntax:

```js
const add = function (a, b) {
  return a + b;
};

const multiply = (a, b) => {
  return a * b;
};

// implicit return for a single expression:
const square = n => n * n;

console.log(add(2, 3));      // 5
console.log(multiply(2, 3)); // 6
console.log(square(4));      // 16
```

Arrow functions are common for short, inline functions (like callbacks passed to array methods in Lesson 13). One key difference from regular functions: arrow functions don't have their own `this` — they inherit it from the surrounding code, which matters once you reach Lesson 22.

---

## 10.3 Default And Rest Parameters

Parameters can have default values, used when no argument (or `undefined`) is passed:

```js
function greet(name = "friend") {
  return `Hello, ${name}!`;
}

greet();       // "Hello, friend!"
greet("Sam");  // "Hello, Sam!"
```

A function can also accept an unlimited number of arguments using the **rest parameter** (`...`), which collects them into an array:

```js
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}

sum(1, 2, 3);       // 6
sum(5, 10, 15, 20); // 50
```

Lesson 15 covers the rest syntax (and its counterpart, spread) in more detail.

---

## 10.4 Hoisting

**Hoisting** means JavaScript moves function and variable *declarations* to the top of their scope before running any code. This lets you call a function declaration before it appears in the file:

```js
sayHi(); // works fine — "Hi!"

function sayHi() {
  console.log("Hi!");
}
```

This does **not** work with function expressions or arrow functions stored in `const`/`let`, since only the variable declaration is hoisted, not its assigned value:

```js
sayHi(); // ReferenceError: Cannot access 'sayHi' before initialization

const sayHi = () => console.log("Hi!");
```

For this reason, most style guides recommend defining functions before they're used, regardless of hoisting.

---

## 10.5 Scope: Where Variables Live

**Scope** determines where in your code a variable is accessible.

- **Global scope** — declared outside any function; accessible everywhere.
- **Function scope** — variables declared with `var` inside a function are accessible anywhere in that function.
- **Block scope** — variables declared with `let`/`const` inside `{ }` (an if-block, loop, etc.) are only accessible within that block.

```js
function demo() {
  if (true) {
    let blockScoped = "only visible in this block";
    var functionScoped = "visible throughout the function";
  }
  console.log(functionScoped);  // works
  console.log(blockScoped);      // ReferenceError
}
```

This is the core reason to prefer `let`/`const` over `var`: block scoping is more predictable and avoids variables "leaking" outside the block they were meant for.

---

## 10.6 Closures

A **closure** happens when a function "remembers" variables from the scope it was created in, even after that outer function has finished running:

```js
function makeCounter() {
  let count = 0;
  return function () {
    count++;
    return count;
  };
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3
```

Each call to `counter()` still has access to `count`, even though `makeCounter()` already finished executing. Closures are the mechanism behind many patterns you'll use later, including data privacy in Lesson 21.

[Previous](./%5B9%5D-Loops%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[11]-String-Formatting.md)
