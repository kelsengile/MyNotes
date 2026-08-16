[Previous](./[32]-Interfaces-with-Classes.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[34]-Decorators.md)

*Object-Oriented TypeScript*

# Lesson 33 - Parameter Properties And Constructor Shorthand

## 33.1 The Verbose Way

So far, declaring a class property has taken three steps: declare the field, accept it as a constructor parameter, and assign it inside the constructor body:

```ts
class Product {
  name: string;
  price: number;

  constructor(name: string, price: number) {
    this.name = name;
    this.price = price;
  }
}
```

This works fine, but it's repetitive, especially as classes grow more properties.

---

## 33.2 Parameter Properties Shorthand

TypeScript lets you collapse all three steps into one by adding an access modifier directly to a constructor parameter. This automatically declares the property on the class and assigns it from the argument:

```ts
class Product {
  constructor(public name: string, public price: number) {}
}

const item = new Product("Keyboard", 49.99);
console.log(item.name);  // "Keyboard"
console.log(item.price); // 49.99
```

This is called a **parameter property**, and it behaves identically to the verbose version — it's purely a shorthand.

---

## 33.3 Mixing Access Modifiers in Parameter Properties

Each constructor parameter can use a different access modifier (`public`, `private`, `protected`) or `readonly`, and you can combine `readonly` with an access modifier:

```ts
class Product {
  constructor(
    public name: string,
    private price: number,
    readonly sku: string,
    protected readonly category: string
  ) {}

  getPrice(): number {
    return this.price;
  }
}
```

Parameters without a modifier are treated as plain constructor parameters, not properties — they won't be attached to `this` unless you assign them manually:

```ts
class Logger {
  constructor(prefix: string, public level: string) {
    // 'prefix' is just a local variable here, not a property
    console.log(`${prefix}: logger created`);
  }
}
```

---

## 33.4 When Not to Use Shorthand

Parameter properties are best for simple, direct assignments. If a property needs validation, transformation, or a default computed from other parameters, it's clearer to fall back to the verbose form:

```ts
class Rectangle {
  area: number;

  constructor(public width: number, public height: number) {
    if (width <= 0 || height <= 0) {
      throw new Error("Width and height must be positive");
    }
    this.area = width * height;
  }
}
```

Mixing both styles in the same constructor, as shown above, is completely valid — use shorthand where it's a direct assignment, and the constructor body for anything that needs extra logic.

[Previous](./[32]-Interfaces-with-Classes.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[34]-Decorators.md)
