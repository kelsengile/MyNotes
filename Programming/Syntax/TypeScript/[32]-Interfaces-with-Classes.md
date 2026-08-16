[Previous](./[31]-Readonly-Static-and-Abstract-Members.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[33]-Parameter-Properties.md)

*Object-Oriented TypeScript*

# Lesson 32 - Interfaces With Classes (`implements`)

## 32.1 Implementing an Interface

While `extends` (used with `abstract` classes in [Lesson 31](./[31]-Readonly-Static-and-Abstract-Members.md)) shares implementation, `implements` only enforces a *shape*. A class that `implements` an interface must provide every property and method the interface describes, but the interface itself contains no code.

```ts
interface Printable {
  print(): void;
}

class Invoice implements Printable {
  constructor(private amount: number) {}

  print(): void {
    console.log(`Invoice amount: $${this.amount}`);
  }
}
```

If `Invoice` left out `print()`, or gave it the wrong signature, TypeScript would report an error at the class declaration.

---

## 32.2 Implementing Multiple Interfaces

A class can implement more than one interface by separating them with commas. This is useful for composing independent capabilities:

```ts
interface Serializable {
  serialize(): string;
}

interface Printable {
  print(): void;
}

class Report implements Serializable, Printable {
  constructor(private title: string) {}

  serialize(): string {
    return JSON.stringify({ title: this.title });
  }

  print(): void {
    console.log(this.title);
  }
}
```

`Report` must now satisfy the full combined shape of both interfaces.

---

## 32.3 Interfaces vs Abstract Classes for Contracts

Both interfaces and abstract classes can define a contract that other classes must follow, but they solve slightly different problems:

| | Interface | Abstract Class |
|---|---|---|
| Shared implementation | No | Yes |
| Multiple inheritance | Yes (implement many) | No (extend only one) |
| Constructors | No | Yes |
| Access modifiers on members | No | Yes |

Use an interface when you only need to describe a shape that unrelated classes might share. Use an abstract class when related classes should also share common implementation, state, or a constructor.

---

## 32.4 Common Pitfalls

A class can `implements` an interface and `extends` another class at the same time:

```ts
abstract class Entity {
  constructor(public id: number) {}
}

interface Printable {
  print(): void;
}

class User extends Entity implements Printable {
  constructor(id: number, private name: string) {
    super(id);
  }

  print(): void {
    console.log(`User #${this.id}: ${this.name}`);
  }
}
```

A common mistake is expecting `implements` to copy default behavior from the interface — it never does, since interfaces have no runtime representation at all. They exist purely for compile-time checking and disappear entirely once compiled to JavaScript.

[Previous](./[31]-Readonly-Static-and-Abstract-Members.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[33]-Parameter-Properties.md)
