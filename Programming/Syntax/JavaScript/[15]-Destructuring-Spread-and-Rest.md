[Previous](./[14]-Objects-and-Object-Methods.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[16]-Sets-and-Maps.md)

*Data Structures*

# Lesson 15 - Destructuring, Spread And Rest

## 15.1 Array Destructuring

**Destructuring** unpacks values from an array or object into individual variables in one step:

```js
const coordinates = [10, 20, 30];

const [x, y, z] = coordinates;
console.log(x, y, z); // 10 20 30
```

You can skip items and provide default values:

```js
const [first, , third] = [1, 2, 3];
console.log(first, third); // 1 3

const [a = 0, b = 0] = [5];
console.log(a, b); // 5 0
```

Swapping two variables becomes a one-liner:

```js
let m = 1, n = 2;
[m, n] = [n, m];
console.log(m, n); // 2 1
```

---

## 15.2 Object Destructuring

```js
const user = { name: "Priya", age: 28, city: "Pune" };

const { name, age } = user;
console.log(name, age); // "Priya" 28
```

Rename a variable while destructuring, and provide defaults for missing keys:

```js
const { name: userName, country = "Unknown" } = user;
console.log(userName, country); // "Priya" "Unknown"
```

Destructuring is especially common for function parameters, so a function can pull out just the fields it needs from an object argument:

```js
function printUser({ name, age }) {
  console.log(`${name} is ${age}`);
}

printUser(user); // "Priya is 28"
```

---

## 15.3 The Spread Operator (...)

**Spread** expands an array or object into its individual elements — commonly used to copy or combine data without mutating the originals:

```js
const nums = [1, 2, 3];
const copy = [...nums];              // [1, 2, 3] — a new array
const combined = [...nums, 4, 5];   // [1, 2, 3, 4, 5]

const arr1 = [1, 2];
const arr2 = [3, 4];
const merged = [...arr1, ...arr2];  // [1, 2, 3, 4]
```

The same idea works for objects:

```js
const defaults = { theme: "light", size: "medium" };
const overrides = { theme: "dark" };

const settings = { ...defaults, ...overrides };
console.log(settings); // { theme: "dark", size: "medium" }
```

When keys overlap, later sources win — this is a cleaner alternative to `Object.assign()` from Lesson 14.

Spread is also used to pass an array as individual arguments to a function:

```js
function sum(a, b, c) {
  return a + b + c;
}

const values = [1, 2, 3];
sum(...values); // 6
```

---

## 15.4 The Rest Parameter (...)

**Rest** looks identical to spread but does the opposite — it *collects* multiple items into a single array. It appears in function parameters (introduced in Lesson 10) and in destructuring:

```js
function sum(...numbers) {
  return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4); // 10

const [first, ...rest] = [1, 2, 3, 4];
console.log(first, rest); // 1 [2, 3, 4]

const { name, ...otherFields } = { name: "Priya", age: 28, city: "Pune" };
console.log(name, otherFields); // "Priya" { age: 28, city: "Pune" }
```

**The distinction:** spread *expands* a collection into individual values (used where multiple values are expected); rest *gathers* individual values into a collection (used in a declaration to catch "everything else").

[Previous](./[14]-Objects-and-Object-Methods.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[16]-Sets-and-Maps.md)
