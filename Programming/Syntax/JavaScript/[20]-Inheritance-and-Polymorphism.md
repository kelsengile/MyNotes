[Previous](./[19]-Prototypes-and-Prototypal-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[21]-Encapsulation.md)

*Object-Oriented Programming*

# Lesson 20 - Inheritance And Polymorphism

## 20.1 What Is Inheritance?

**Inheritance** lets one class build on another, reusing its properties and methods instead of duplicating them. The class being built upon is the **parent** (or superclass); the class extending it is the **child** (or subclass).

```js
class Animal {
  constructor(name) {
    this.name = name;
  }

  speak() {
    return `${this.name} makes a sound.`;
  }
}

class Dog extends Animal {
  bark() {
    return `${this.name} barks!`;
  }
}

const rex = new Dog("Rex");
console.log(rex.speak()); // "Rex makes a sound." — inherited from Animal
console.log(rex.bark());   // "Rex barks!"          — defined on Dog
```

`extends` sets up the inheritance link; `Dog` automatically gets everything `Animal` has, plus whatever it defines itself.

---

## 20.2 The super Keyword

When a subclass defines its own `constructor`, it must call `super()` first, to run the parent class's constructor logic:

```js
class Animal {
  constructor(name) {
    this.name = name;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // calls Animal's constructor, sets this.name
    this.breed = breed;
  }
}

const rex = new Dog("Rex", "Labrador");
console.log(rex.name, rex.breed); // "Rex" "Labrador"
```

`super` can also call a parent's *method* from within an overridden version of it (see the next section).

---

## 20.3 Overriding Methods

A subclass can redefine a method it inherits, replacing the parent's behavior entirely — or extending it with `super.methodName()`:

```js
class Animal {
  speak() {
    return "Some generic animal sound";
  }
}

class Dog extends Animal {
  speak() {
    const base = super.speak(); // call the parent version too
    return `${base}, specifically: Woof!`;
  }
}

const rex = new Dog();
console.log(rex.speak()); // "Some generic animal sound, specifically: Woof!"
```

---

## 20.4 Polymorphism

**Polymorphism** ("many forms") means different classes can respond to the same method call in their own way. This lets you write code that works with a general type without caring about the specific subclass:

```js
class Animal {
  speak() {
    return "...";
  }
}

class Dog extends Animal {
  speak() {
    return "Woof!";
  }
}

class Cat extends Animal {
  speak() {
    return "Meow!";
  }
}

const animals = [new Dog(), new Cat(), new Animal()];

for (const animal of animals) {
  console.log(animal.speak());
}
// "Woof!"
// "Meow!"
// "..."
```

The loop doesn't need to know or check which specific class each `animal` is — it simply calls `.speak()` and trusts each object to handle it correctly. This is one of the most powerful ideas in OOP: code written against a general shape (`Animal`) automatically works with any more specific version of it.

---

## 20.5 When To Use Inheritance

Inheritance works best for a genuine **"is-a" relationship** — a `Dog` *is an* `Animal`, a `Manager` *is an* `Employee`. If two classes only share some unrelated behavior without a true is-a relationship, inheritance can create rigid, awkward hierarchies. Lesson 23 introduces **mixins and composition** as a more flexible alternative for sharing behavior across unrelated classes.

[Previous](./[19]-Prototypes-and-Prototypal-Inheritance.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[21]-Encapsulation.md)
