[Previous](./[21]-Encapsulation.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[23]-Mixins-and-Composition.md)

*Object-Oriented Programming*

# Lesson 22 - Static Members And The `this` Keyword

## 22.1 Static Properties And Methods

A **static** member belongs to the class itself, not to any individual instance. It's used for utility functions or data related to the class as a whole rather than any one object:

```js
class Circle {
  static pi = 3.14159;

  constructor(radius) {
    this.radius = radius;
  }

  area() {
    return Circle.pi * this.radius ** 2;
  }

  static compare(circleA, circleB) {
    return circleA.area() - circleB.area();
  }
}

const a = new Circle(5);
console.log(a.area());        // 78.53975
console.log(Circle.pi);        // 3.14159 — accessed on the class, not an instance
console.log(a.pi);              // undefined — not available on instances

const b = new Circle(3);
console.log(Circle.compare(a, b)); // positive — a is larger
```

Built-in examples you've already used include `Object.keys()` (Lesson 14) and `Array.isArray()` (Lesson 13) — both are static methods on `Object` and `Array`.

---

## 22.2 A Common Use: Factory Methods

Static methods are often used to create instances in specific, named ways:

```js
class User {
  constructor(name, role) {
    this.name = name;
    this.role = role;
  }

  static createGuest() {
    return new User("Guest", "guest");
  }

  static createAdmin(name) {
    return new User(name, "admin");
  }
}

const guest = User.createGuest();
const admin = User.createAdmin("Wei");
```

---

## 22.3 What `this` Refers To

`this` refers to whatever object a function was called *on*. Its value depends on **how** a function is called, not where it was defined:

```js
const person = {
  name: "Lee",
  greet() {
    return `Hi, I'm ${this.name}`;
  },
};

person.greet(); // "Hi, I'm Lee" — `this` is `person`, since called as person.greet()

const greetFn = person.greet;
greetFn(); // "Hi, I'm undefined" — called alone, `this` is no longer `person`
```

This is one of the most common sources of confusion in JavaScript: the same function can behave differently depending on how it's invoked.

---

## 22.4 `this` Inside Regular Functions vs. Arrow Functions

Arrow functions (Lesson 10) don't have their own `this` — they use `this` from wherever they were defined (their surrounding, "lexical" scope). This makes them useful in exactly the cases where regular functions cause `this`-related bugs:

```js
class Timer {
  seconds = 0;

  start() {
    setInterval(function () {
      this.seconds++; // BUG: `this` here is not the Timer instance
      console.log(this.seconds); // NaN, repeatedly
    }, 1000);
  }
}
```

Fixed with an arrow function, which inherits `this` from `start()`'s surrounding scope:

```js
class Timer {
  seconds = 0;

  start() {
    setInterval(() => {
      this.seconds++; // `this` correctly refers to the Timer instance
      console.log(this.seconds); // 1, 2, 3, ...
    }, 1000);
  }
}
```

---

## 22.5 Explicitly Controlling `this`: call, apply, bind

Three methods let you set `this` manually:

```js
function greet() {
  return `Hi, I'm ${this.name}`;
}

const person = { name: "Amara" };

greet.call(person);        // "Hi, I'm Amara" — calls immediately, `this` set to person
greet.apply(person);       // same as call, but takes arguments as an array

const boundGreet = greet.bind(person); // returns a NEW function with `this` locked in
boundGreet(); // "Hi, I'm Amara" — can be called anytime, `this` stays person
```

`bind()` is especially useful when passing a method as a callback (e.g. to `setTimeout`, or as an event handler in Lesson 37) where it would otherwise lose its intended `this`.

[Previous](./[21]-Encapsulation.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[23]-Mixins-and-Composition.md)
