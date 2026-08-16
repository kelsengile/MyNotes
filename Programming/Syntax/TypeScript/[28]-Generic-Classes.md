[Previous](./[27]-Generic-Constraints.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[29]-Default-Generic-Parameters.md)

*Generics*

# Lesson 28 - Generic Classes

## 28.1 Declaring a Generic Class

A class can take a type parameter just like a function or interface, declared right after the class name:

```typescript
class Box<T> {
  private contents: T;

  constructor(value: T) {
    this.contents = value;
  }

  get(): T {
    return this.contents;
  }
}

const numberBox = new Box<number>(42);
const stringBox = new Box("hello"); // T inferred as string from the constructor argument
```

Every method and property inside the class can reference `T`, and it stays consistent throughout a single instance.

---

## 28.2 A Practical Example: A Generic Stack

```typescript
class Stack<T> {
  private items: T[] = [];

  push(item: T): void {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }

  peek(): T | undefined {
    return this.items[this.items.length - 1];
  }

  get size(): number {
    return this.items.length;
  }
}

const numbers = new Stack<number>();
numbers.push(1);
numbers.push(2);
numbers.pop(); // 2, typed as number | undefined
```

The same `Stack` class works correctly and safely for `Stack<string>`, `Stack<User>`, or any other type, without rewriting a single line.

---

## 28.3 Constraining a Class's Type Parameter

Just like generic functions (Lesson 27), a generic class's type parameter can be constrained with `extends`:

```typescript
interface Identifiable {
  id: string;
}

class Repository<T extends Identifiable> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  findById(id: string): T | undefined {
    return this.items.find((item) => item.id === id);
  }
}
```

Now `Repository` can only be used with types that have an `id: string`, which the `findById` implementation relies on.

---

## 28.4 Multiple Type Parameters in a Class

Classes can take more than one type parameter, useful for structures that relate two different types, like a typed key-value store:

```typescript
class Dictionary<K, V> {
  private map = new Map<K, V>();

  set(key: K, value: V): void {
    this.map.set(key, value);
  }

  get(key: K): V | undefined {
    return this.map.get(key);
  }
}

const scores = new Dictionary<string, number>();
scores.set("amy", 95);
```

---

## 28.5 Generic Methods Inside a Non-Generic Class

A method can introduce its own type parameter even when the surrounding class isn't generic, scoped only to that one method:

```typescript
class Utils {
  static wrapInArray<T>(value: T): T[] {
    return [value];
  }
}

Utils.wrapInArray("hello"); // string[]
Utils.wrapInArray(42);      // number[]
```

This is useful for one-off generic behavior that doesn't warrant making the entire class generic.

---

[Previous](./[27]-Generic-Constraints.md) | [Table of Contents](./[0]-Introduction-to-TypeScript.md) | [Next](./[29]-Default-Generic-Parameters.md)
