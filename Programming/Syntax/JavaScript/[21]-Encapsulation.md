[Previous](./[20]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[22]-Static-Members-and-This.md)

*Object-Oriented Programming*

# Lesson 21 - Encapsulation

## 21.1 What Is Encapsulation?

**Encapsulation** means keeping an object's internal details hidden and controlling access to them through a defined interface (its public methods), rather than letting outside code freely read or modify internal state. This protects data from being changed in invalid ways and lets you change internal implementation later without breaking code that uses the object.

```js
class BankAccount {
  balance = 0;

  deposit(amount) {
    if (amount <= 0) throw new Error("Deposit must be positive");
    this.balance += amount;
  }
}

const account = new BankAccount();
account.deposit(100);
account.balance = -1000; // nothing stops this — balance isn't actually protected
```

Without encapsulation, anyone can bypass `deposit()`'s validation and set `balance` directly to an invalid value.

---

## 21.2 Private Fields

Modern JavaScript classes support truly **private fields**, prefixed with `#`, which are only accessible from inside the class itself:

```js
class BankAccount {
  #balance = 0; // private — inaccessible outside this class

  deposit(amount) {
    if (amount <= 0) throw new Error("Deposit must be positive");
    this.#balance += amount;
  }

  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount();
account.deposit(100);
console.log(account.getBalance()); // 100

account.#balance = -1000; // SyntaxError — #balance isn't accessible here
```

Private fields must be declared in the class body (they can't be created dynamically like regular properties), and the `#` is part of the name itself.

---

## 21.3 Private Methods

Methods can be made private the same way — useful for internal helper logic that shouldn't be part of the class's public interface:

```js
class BankAccount {
  #balance = 0;

  #validate(amount) {
    if (amount <= 0) throw new Error("Amount must be positive");
  }

  deposit(amount) {
    this.#validate(amount);
    this.#balance += amount;
  }

  getBalance() {
    return this.#balance;
  }
}
```

`#validate` can only be called from within `BankAccount`'s own methods — external code can't call `account.#validate(10)`.

---

## 21.4 Getters And Setters

**Getters** and **setters** let you define properties that run code when read or written, while still looking like plain property access from the outside:

```js
class BankAccount {
  #balance = 0;

  get balance() {
    return this.#balance;
  }

  set balance(amount) {
    if (amount < 0) throw new Error("Balance cannot be negative");
    this.#balance = amount;
  }
}

const account = new BankAccount();
account.balance = 100;      // calls the setter
console.log(account.balance); // 100 — calls the getter

account.balance = -50; // throws Error: Balance cannot be negative
```

This gives you validation and control (like a method) while keeping the simple syntax of a property (no parentheses needed to read or write it).

---

## 21.5 Encapsulation Through Closures (The Pre-# Pattern)

Before private class fields existed, developers achieved similar privacy using **closures** (Lesson 10) — variables scoped to a function that an inner function retains access to:

```js
function createBankAccount() {
  let balance = 0; // trapped inside this function's scope

  return {
    deposit(amount) {
      if (amount <= 0) throw new Error("Amount must be positive");
      balance += amount;
    },
    getBalance() {
      return balance;
    },
  };
}

const account = createBankAccount();
account.deposit(100);
console.log(account.getBalance()); // 100
console.log(account.balance);       // undefined — not accessible at all
```

This pattern still appears often in real-world code, particularly outside of classes, so it's worth recognizing even though `#` private fields are now the standard approach within classes.

[Previous](./[20]-Inheritance-and-Polymorphism.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[22]-Static-Members-and-This.md)
