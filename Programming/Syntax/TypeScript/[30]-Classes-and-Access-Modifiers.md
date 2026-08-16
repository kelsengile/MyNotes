[Previous](./[29]-Default-Generic-Parameters.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[31]-Readonly-Static-and-Abstract-Members.md)

*Object-Oriented TypeScript*

# Lesson 30 - Classes And Access Modifiers

## 30.1 Declaring a Class

TypeScript classes look just like JavaScript classes, but every property can be given a type:

```ts
class Car {
  make: string;
  model: string;
  year: number;

  constructor(make: string, model: string, year: number) {
    this.make = make;
    this.model = model;
    this.year = year;
  }

  describe(): string {
    return `${this.year} ${this.make} ${this.model}`;
  }
}

const myCar = new Car("Toyota", "Corolla", 2022);
console.log(myCar.describe()); // "2022 Toyota Corolla"
```

If you try to assign the wrong type to a property, or call `describe()` with the wrong arguments, TypeScript catches it before your code ever runs.

---

## 30.2 Constructors and Properties

Every property used in a class must be declared (either explicitly, like above, or implicitly through a parameter property — covered in [Lesson 33](./[33]-Parameter-Properties.md)). TypeScript will not let you use a property that hasn't been declared:

```ts
class Point {
  x: number;
  y: number;

  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}
```

If you forget to initialize a declared property in the constructor, TypeScript's `strictPropertyInitialization` flag (on by default under `strict` mode — see [Lesson 52](./[52]-Strict-Mode-and-Compiler-Flags.md)) will raise an error.

---

## 30.3 Access Modifiers: public, private, protected

TypeScript adds three access modifiers that control where a property or method can be used:

- **`public`** (default) — accessible from anywhere.
- **`private`** — accessible only within the declaring class.
- **`protected`** — accessible within the declaring class and its subclasses.

```ts
class BankAccount {
  public owner: string;
  private balance: number;
  protected accountType: string;

  constructor(owner: string, balance: number) {
    this.owner = owner;
    this.balance = balance;
    this.accountType = "standard";
  }

  public deposit(amount: number): void {
    this.balance += amount;
  }

  private logTransaction(amount: number): void {
    console.log(`Transaction: ${amount}`);
  }
}

const account = new BankAccount("Alex", 100);
account.deposit(50);      // OK, public
account.balance;          // Error: 'balance' is private
account.logTransaction(); // Error: 'logTransaction' is private
```

A subclass can access `protected` members but not `private` ones:

```ts
class SavingsAccount extends BankAccount {
  showType(): string {
    return this.accountType; // OK, protected
    // return this.balance;  // Error, private
  }
}
```

These modifiers only exist at compile time — they don't change the emitted JavaScript's runtime behavior (unless you use the newer `#private` fields, which *are* enforced at runtime, but that's a JavaScript feature outside the scope of this lesson).

---

## 30.4 Why Access Modifiers Matter

Access modifiers let you design a class's public API deliberately. Internal details (like `balance` or `logTransaction`) stay hidden, so consumers of the class can only interact with it in the ways you intend. This reduces bugs caused by code reaching into internals it shouldn't touch, and makes it safer to change internal implementation details later without breaking other code.

[Previous](./[29]-Default-Generic-Parameters.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[31]-Readonly-Static-and-Abstract-Members.md)
