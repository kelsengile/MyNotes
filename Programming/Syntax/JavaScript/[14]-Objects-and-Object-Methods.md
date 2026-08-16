[Previous](./[13]-Arrays-and-Array-Methods.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[15]-Destructuring-Spread-and-Rest.md)

*Data Structures*

# Lesson 14 - Objects And Object Methods

## 14.1 Creating And Accessing Objects

An **object** stores data as key-value pairs:

```js
const user = {
  name: "Diego",
  age: 32,
  isAdmin: false,
};

user.name;        // "Diego"      — dot notation
user["age"];      // 32           — bracket notation
```

Use bracket notation when the key is dynamic or stored in a variable:

```js
const key = "name";
user[key]; // "Diego"
```

---

## 14.2 Adding, Updating, And Deleting Properties

```js
const user = { name: "Diego" };

user.age = 32;          // add
user.name = "Diego M."; // update
delete user.age;         // remove

console.log(user); // { name: "Diego M." }
```

---

## 14.3 Methods: Functions Inside Objects

A function stored as an object property is called a **method**:

```js
const person = {
  name: "Kim",
  greet() {
    return `Hi, I'm ${this.name}`;
  },
};

person.greet(); // "Hi, I'm Kim"
```

`this` inside a method refers to the object the method was called on — Lesson 22 covers `this` in full detail, including cases where it behaves unexpectedly.

---

## 14.4 Nested Objects

Objects can contain other objects and arrays, forming deeper structures:

```js
const company = {
  name: "Acme Inc.",
  address: {
    city: "Manila",
    zip: "1000",
  },
  employees: ["Ana", "Ben", "Cid"],
};

company.address.city;      // "Manila"
company.employees[1];      // "Ben"
```

---

## 14.5 Checking Properties

```js
const user = { name: "Diego", age: 32 };

"name" in user;                 // true
user.hasOwnProperty("age");    // true
user.email === undefined;      // true — key doesn't exist
```

`"key" in object` also checks inherited properties (from the prototype chain, covered in Lesson 19), while `hasOwnProperty` checks only properties defined directly on the object.

---

## 14.6 Useful Object Static Methods

```js
const user = { name: "Diego", age: 32 };

Object.keys(user);     // ["name", "age"]
Object.values(user);   // ["Diego", 32]
Object.entries(user);  // [["name", "Diego"], ["age", 32]]

// Looping with entries:
for (const [key, value] of Object.entries(user)) {
  console.log(`${key}: ${value}`);
}
```

`Object.assign()` copies properties from one or more objects into a target:

```js
const defaults = { theme: "light", notifications: true };
const overrides = { theme: "dark" };

const settings = Object.assign({}, defaults, overrides);
console.log(settings); // { theme: "dark", notifications: true }
```

Passing `{}` as the first argument avoids mutating `defaults`. Lesson 15 introduces the spread operator, which offers a more common shorthand for this same merging pattern.

---

## 14.7 Shorthand Property And Method Syntax

When a variable's name matches the key you want, you can skip repeating it:

```js
const name = "Diego";
const age = 32;

// Longhand:
const user1 = { name: name, age: age };

// Shorthand:
const user2 = { name, age };
```

Method definitions can also drop the `function` keyword, as shown in section 14.3 (`greet() { ... }` instead of `greet: function() { ... }`).

[Previous](./[13]-Arrays-and-Array-Methods.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[15]-Destructuring-Spread-and-Rest.md)
