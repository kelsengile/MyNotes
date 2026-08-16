[Previous](./[17]-Type-Aliases.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[19]-Extending-Interfaces-and-Declaration-Merging.md)

*Interfaces & Type Aliases*

# Lesson 18 - Interfaces Vs. Type Aliases: When To Use Which

## 18.1 What They Have in Common

For plain object shapes, `interface` and `type` are nearly interchangeable — both describe properties, both support optional and readonly members, and both can be used to type function parameters:

```typescript
interface UserA { name: string; age: number; }
type UserB = { name: string; age: number; };
```

Code written against either will behave identically in almost all everyday cases.

---

## 18.2 What Only Type Aliases Can Do

As shown in Lesson 17, `type` can name things an `interface` fundamentally cannot:

```typescript
type ID = string | number;               // unions
type Coordinates = [number, number];     // tuples
type Handler = (event: Event) => void;   // standalone function types
type Nullable<T> = T | null;             // utility-style generics
```

If the type you're naming isn't a plain object or function shape, `type` is the only option.

---

## 18.3 What Only Interfaces Can Do: Declaration Merging

Interfaces with the same name in the same scope automatically merge their members together — a feature covered fully in Lesson 19:

```typescript
interface Window {
  myGlobal: string;
}

interface Window {
  anotherGlobal: number;
}

// Window now has both myGlobal and anotherGlobal
```

Type aliases cannot do this — declaring `type Window = {...}` twice in the same scope is a compile error. This makes interfaces especially useful for describing shapes that other code (like a library) might need to extend.

---

## 18.4 Extending Shapes

Both support extension, but with different syntax. Interfaces use `extends`; type aliases use `&` (intersection, from Lesson 17):

```typescript
interface Animal { name: string; }
interface Dog extends Animal { breed: string; }

type AnimalT = { name: string; };
type DogT = AnimalT & { breed: string; };
```

Interface `extends` tends to produce clearer error messages and better editor performance on very large, deeply-extended shapes.

---

## 18.5 A Practical Rule of Thumb

The official TypeScript team guidance, echoed by most style guides:

- Use **`interface`** for public object shapes meant to be extended or implemented — especially the public API of a class or library (`implements`, from Lesson 32).
- Use **`type`** for everything else: unions, tuples, function types, mapped/conditional types, and one-off shapes that don't need to merge or extend.
- Above all, **be consistent** within a single codebase — mixing both for no particular reason adds cognitive overhead without any real benefit.

---

[Previous](./[17]-Type-Aliases.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[19]-Extending-Interfaces-and-Declaration-Merging.md)
