[Previous](./[18]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[20]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 19 - Prototypes And Prototypal Inheritance

## 19.1 Classes Are "Syntactic Sugar"

Lesson 18 introduced `class` syntax, but under the hood, JavaScript doesn't have "real" classes the way languages like Java do. Instead, JavaScript uses **prototypes** — objects can be linked to other objects, and property/method lookups fall back to that link when something isn't found directly on the object itself. `class` is a cleaner syntax for working with this same underlying system.

---

## 19.2 What Is A Prototype?

Every object in JavaScript has an internal link to another object, called its **prototype**. When you access a property or method, JavaScript first checks the object itself; if it's not there, it checks the prototype; if not there, it checks *that* object's prototype, and so on — this chain is called the **prototype chain**.

```js
const animal = {
  eat() {
    return "eating...";
  },
};

const dog = Object.create(animal); // dog's prototype is `animal`
dog.bark = function () {
  return "woof!";
};

console.log(dog.bark()); // "woof!"     — found directly on dog
console.log(dog.eat());  // "eating..." — not on dog, found on its prototype
```

---

## 19.3 How Classes Use Prototypes

When you define a method inside a `class`, JavaScript actually stores it on the class's `prototype` object — a single shared copy, not a separate copy per instance:

```js
class Person {
  constructor(name) {
    this.name = name;
  }
  greet() {
    return `Hi, I'm ${this.name}`;
  }
}

const alice = new Person("Alice");
const bob = new Person("Bob");

console.log(alice.greet === bob.greet); // true — same function, shared via the prototype
console.log(Object.getPrototypeOf(alice) === Person.prototype); // true
```

This is why the number of instances doesn't multiply memory usage for methods — every instance shares one copy through the prototype chain, while properties like `name` are unique to each instance.

---

## 19.4 The Pre-Class Pattern: Constructor Functions

Before `class` syntax existed (introduced in ES2015), developers achieved the same result with regular functions and manually attaching methods to `.prototype`:

```js
function Person(name) {
  this.name = name;
}

Person.prototype.greet = function () {
  return `Hi, I'm ${this.name}`;
};

const alice = new Person("Alice");
console.log(alice.greet()); // "Hi, I'm Alice"
```

This is functionally equivalent to the `class` version above. You'll still encounter this older pattern in existing codebases and tutorials, so it's worth recognizing even though `class` syntax is preferred for new code.

---

## 19.5 Checking The Prototype Chain

```js
console.log(Object.getPrototypeOf(alice) === Person.prototype); // true
console.log(alice instanceof Person);                              // true

console.log(Object.getPrototypeOf({}) === Object.prototype);     // true — plain objects link to Object.prototype
console.log(Object.getPrototypeOf(Object.prototype));              // null — the end of the chain
```

Every plain object's prototype chain eventually ends at `Object.prototype`, then `null`. This is also where common methods like `.hasOwnProperty()` and `.toString()` actually come from — they're inherited, not built into every object individually.

[Previous](./[18]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[20]-Inheritance-and-Polymorphism.md)
