[Previous](./[18]-Interfaces-vs-Type-Aliases.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[20]-Readonly-and-Optional-Properties.md)

*Interfaces & Type Aliases*

# Lesson 19 - Extending Interfaces And Declaration Merging

## 19.1 Basic Extension with `extends`

An interface can build on another using `extends`, inheriting all of its members and adding more:

```typescript
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

const rex: Dog = { name: "Rex", breed: "Labrador" };
```

`Dog` now requires both `name` and `breed` — every member of `Animal`, plus its own.

---

## 19.2 Extending Multiple Interfaces

An interface can extend more than one parent, combining all of their members:

```typescript
interface Named {
  name: string;
}

interface Aged {
  age: number;
}

interface Person extends Named, Aged {
  email: string;
}
```

`Person` requires `name`, `age`, and `email` — every property from every extended interface.

---

## 19.3 Overriding a Property

A child interface can narrow an inherited property's type, as long as the new type is still compatible with the original:

```typescript
interface Shape {
  color: string | null;
}

interface RedShape extends Shape {
  color: "red"; // narrowed, still assignable to string | null
}
```

Attempting to widen or change a property to an incompatible type causes a compile error, since it would break the "is-a" relationship the extension implies.

---

## 19.4 Declaration Merging

Unlike a `type` alias, declaring two interfaces with the **same name** in the same scope automatically merges them into one combined interface:

```typescript
interface Config {
  debug: boolean;
}

interface Config {
  retries: number;
}

// Equivalent to a single interface with both properties:
const settings: Config = { debug: true, retries: 3 };
```

This isn't something you'd usually do deliberately within your own code — it matters most when *extending types you don't own*.

---

## 19.5 Practical Use: Augmenting Third-Party Types

Declaration merging is the standard technique for adding properties to types defined by a library or the runtime environment, such as attaching custom data to Express's `Request` object:

```typescript
declare global {
  namespace Express {
    interface Request {
      userId?: string;
    }
  }
}

// Elsewhere in the app, req.userId is now recognized without extra casting
app.use((req, res, next) => {
  req.userId = "123";
  next();
});
```

This pattern — often placed in a `.d.ts` ambient declaration file (Lesson 43) — lets you extend well-known interfaces from a library without modifying its source code.

---

[Previous](./[18]-Interfaces-vs-Type-Aliases.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[20]-Readonly-and-Optional-Properties.md)
