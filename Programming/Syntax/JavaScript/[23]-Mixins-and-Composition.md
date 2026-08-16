[Previous](./[22]-Static-Members-and-This.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md)

*Object-Oriented Programming*

# Lesson 23 - Mixins And Composition

## 23.1 The Limits Of Single Inheritance

JavaScript classes can only `extend` **one** parent class at a time. This is fine for a clean "is-a" hierarchy (Lesson 20), but it breaks down when an object needs to share behavior from multiple, unrelated sources. For example, both a `Bird` and an `Airplane` can "fly," and both a `Duck` and a `Boat` can "swim" — but a `Duck` is not an `Airplane`, so normal inheritance can't cleanly express "this class can fly *and* swim."

---

## 23.2 What Is A Mixin?

A **mixin** is a way to add reusable behavior to a class without using inheritance. In JavaScript, a common pattern is a function that takes a class and returns a new class extending it with extra methods:

```js
const CanFly = Base => class extends Base {
  fly() {
    return `${this.name} is flying!`;
  }
};

const CanSwim = Base => class extends Base {
  swim() {
    return `${this.name} is swimming!`;
  }
};

class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Duck extends CanFly(CanSwim(Animal)) {}

const donald = new Duck("Donald");
console.log(donald.fly());  // "Donald is flying!"
console.log(donald.swim()); // "Donald is swimming!"
```

`Duck` combines behavior from `Animal`, `CanFly`, and `CanSwim` — none of which are related to each other through a strict "is-a" hierarchy.

---

## 23.3 Composition Over Inheritance

**Composition** takes this a step further: instead of building up classes through layers of inheritance, you build objects by combining smaller, independent pieces — often plain objects or functions — at the point where you assemble the final object.

```js
const canFly = {
  fly() {
    return `${this.name} is flying!`;
  },
};

const canSwim = {
  swim() {
    return `${this.name} is swimming!`;
  },
};

function createDuck(name) {
  return Object.assign({ name }, canFly, canSwim);
}

const donald = createDuck("Donald");
console.log(donald.fly());  // "Donald is flying!"
console.log(donald.swim()); // "Donald is swimming!"
```

This is often summarized as the principle **"favor composition over inheritance"**: rather than asking "what is this thing?" (inheritance), you ask "what can this thing do?" and assemble those capabilities directly. It tends to produce more flexible code, since new capabilities can be added without reshaping an entire class hierarchy.

---

## 23.4 Composition With Object Spread

Lesson 15's spread operator offers an even more concise way to compose behavior into a new object:

```js
const canFly = { fly() { return `${this.name} flies`; } };
const canSwim = { swim() { return `${this.name} swims`; } };

function createDuck(name) {
  return { name, ...canFly, ...canSwim };
}
```

This produces the same result as `Object.assign()` in the previous section — spread is simply the more modern syntax for merging objects together.

---

## 23.5 Choosing Between Inheritance, Mixins, And Composition

| Approach | Best for |
|---|---|
| **Inheritance** (`extends`) | A clear, singular "is-a" relationship with a natural hierarchy (Lesson 20) |
| **Mixins** | Adding a specific chunk of reusable behavior into an existing class hierarchy |
| **Composition** | Assembling independent, unrelated capabilities — most flexible, least rigid |

There's no universally "correct" choice — many real applications use all three depending on the situation. A good rule of thumb: reach for inheritance first when the relationship is genuinely hierarchical, and reach for composition when you find yourself trying to force unrelated behaviors into an awkward class hierarchy just to reuse code.

---

This wraps up the **Object-Oriented Programming** unit. From here, the course continues into **Asynchronous JavaScript** — starting with callbacks, then promises and `async`/`await` — building on the functions and scope concepts from Lesson 10.

[Previous](./[22]-Static-Members-and-This.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md)
