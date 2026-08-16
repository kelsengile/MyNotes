[Previous](./[30]-Classes-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[32]-Interfaces-with-Classes.md)

*Object-Oriented TypeScript*

# Lesson 31 - Readonly, Static And Abstract Members

## 31.1 Readonly Properties

Marking a property `readonly` means it can only be assigned once — either at its declaration or inside the constructor. After that, any attempt to reassign it is a compile-time error.

```ts
class Employee {
  readonly id: number;
  name: string;

  constructor(id: number, name: string) {
    this.id = id;
    this.name = name;
  }
}

const emp = new Employee(1, "Sam");
emp.name = "Samantha"; // OK
emp.id = 2;            // Error: Cannot assign to 'id' because it is read-only
```

`readonly` is useful for values that identify or configure an object and should never drift after creation, like database IDs or creation timestamps.

---

## 31.2 Static Properties and Methods

`static` members belong to the class itself rather than to any instance. They're accessed through the class name, not through `this` on an instance.

```ts
class Counter {
  static count: number = 0;

  constructor() {
    Counter.count++;
  }

  static getCount(): number {
    return Counter.count;
  }
}

new Counter();
new Counter();
console.log(Counter.getCount()); // 2
```

Static members are commonly used for shared configuration, counters, caches, or factory methods that produce instances of the class.

```ts
class Point {
  constructor(public x: number, public y: number) {}

  static origin(): Point {
    return new Point(0, 0);
  }
}

const start = Point.origin();
```

---

## 31.3 Abstract Classes and Methods

An `abstract` class is a class that can't be instantiated directly — it exists to be extended. It can define both regular methods (with implementations) and `abstract` methods (with no implementation), which subclasses are required to implement.

```ts
abstract class Shape {
  abstract area(): number;

  describe(): string {
    return `This shape has an area of ${this.area()}`;
  }
}

class Circle extends Shape {
  constructor(private radius: number) {
    super();
  }

  area(): number {
    return Math.PI * this.radius ** 2;
  }
}

const c = new Circle(2);
console.log(c.describe()); // "This shape has an area of 12.566..."

new Shape(); // Error: Cannot create an instance of an abstract class
```

If `Circle` didn't implement `area()`, TypeScript would raise an error, since `area()` is required by the abstract class.

---

## 31.4 Combining Readonly, Static, and Abstract

These modifiers can be combined with each other and with the access modifiers from [Lesson 30](./[30]-Classes-and-Access-Modifiers.md) to precisely describe how a member should behave:

```ts
abstract class Config {
  protected static readonly VERSION: string = "1.0.0";

  abstract validate(): boolean;
}
```

Here, `VERSION` belongs to the class (`static`), can never be reassigned (`readonly`), and is only visible to `Config` and its subclasses (`protected`). Layering modifiers this way lets you model exactly the guarantees your design needs.

[Previous](./[30]-Classes-and-Access-Modifiers.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[32]-Interfaces-with-Classes.md)
