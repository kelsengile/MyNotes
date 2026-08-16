[Previous](./[17]-JSON.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[19]-Prototypes-and-Prototypal-Inheritance.md)

*Object-Oriented Programming*

# Lesson 18 - Classes And Objects

## 18.1 Why Object-Oriented Programming?

**Object-oriented programming (OOP)** organizes code around objects that bundle related data (**properties**) and behavior (**methods**) together. Instead of scattering variables and functions that operate on them, you model real-world (or conceptual) "things" — a `User`, a `Car`, an `Order` — as self-contained units. This course's next six lessons build up the full OOP toolkit JavaScript offers.

---

## 18.2 Defining A Class

A `class` is a template for creating objects with the same shape:

```js
class Person {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  greet() {
    return `Hi, I'm ${this.name} and I'm ${this.age}.`;
  }
}
```

- `constructor` runs automatically when a new object is created, setting up its initial properties.
- `this` inside the class refers to the specific object being created or used.
- `greet` is a method — shared by every object made from this class.

---

## 18.3 Creating Instances

An object created from a class is called an **instance**, made with the `new` keyword:

```js
const alice = new Person("Alice", 30);
const bob = new Person("Bob", 25);

console.log(alice.greet()); // "Hi, I'm Alice and I'm 30."
console.log(bob.greet());   // "Hi, I'm Bob and I'm 25."

console.log(alice instanceof Person); // true
```

Each instance has its own independent copies of `name` and `age`, but they share the same `greet` method (Lesson 19 explains exactly how that sharing works, under the hood).

---

## 18.4 Object Literals vs. Classes

Lesson 14 covered creating a single object directly with `{ }` (an **object literal**). Classes matter once you need to create *many* objects with the same shape and behavior — a class is a reusable blueprint, while an object literal describes just one specific object:

```js
// One-off object — fine for a single, unique thing:
const config = { theme: "dark", language: "en" };

// Many similar objects — a class avoids repeating the same structure:
class Product {
  constructor(name, price) {
    this.name = name;
    this.price = price;
  }
}

const items = [
  new Product("Pen", 1.5),
  new Product("Notebook", 3.0),
  new Product("Eraser", 0.5),
];
```

---

## 18.5 Methods Can Use Other Properties And Methods

Inside a class, methods commonly reference each other and the instance's own properties through `this`:

```js
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }

  perimeter() {
    return 2 * (this.width + this.height);
  }

  describe() {
    return `A ${this.width}x${this.height} rectangle with area ${this.area()}.`;
  }
}

const rect = new Rectangle(4, 5);
console.log(rect.describe()); // "A 4x5 rectangle with area 20."
```

---

## 18.6 Class Fields (Properties Without A Constructor)

Modern JavaScript allows declaring properties directly in the class body, with or without the constructor:

```js
class Counter {
  count = 0; // class field — set automatically for every instance

  increment() {
    this.count++;
    return this.count;
  }
}

const c = new Counter();
c.increment(); // 1
c.increment(); // 2
```

This is especially handy for default values that don't depend on constructor arguments.

[Previous](./[17]-JSON.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[19]-Prototypes-and-Prototypal-Inheritance.md)
