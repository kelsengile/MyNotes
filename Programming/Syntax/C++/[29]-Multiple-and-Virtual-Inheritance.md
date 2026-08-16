[Previous](./[28]-Abstract-Classes-and-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[30]-Function-Templates.md)

*Object-Oriented Programming*

# Lesson 29 - Multiple And Virtual Inheritance

## 29.1 What Is Multiple Inheritance

Unlike many languages that only allow a class to inherit from one base class, C++ allows a class to inherit from **more than one** base class at once:

```cpp
class Flyable {
public:
    void fly() { std::cout << "Flying\n"; }
};

class Swimmable {
public:
    void swim() { std::cout << "Swimming\n"; }
};

class Duck : public Flyable, public Swimmable {
    // Duck inherits both fly() and swim()
};

Duck d;
d.fly();
d.swim();
```

This is useful for combining independent capabilities (as seen with interface-style classes in the previous lesson), but it introduces complications when the base classes aren't fully independent.

---

## 29.2 The Diamond Problem

The classic complication arises when two base classes both inherit from the **same** further-back base class, forming a "diamond" shape:

```cpp
class Animal {
public:
    std::string name;
};

class Flyable : public Animal {};
class Swimmable : public Animal {};

class Duck : public Flyable, public Swimmable {};

Duck d;
// d.name = "Donald"; // compile error: ambiguous!
```

Without any special handling, `Duck` ends up with **two separate copies** of `Animal` — one through `Flyable`, one through `Swimmable` — so `d.name` is ambiguous: the compiler doesn't know which copy you mean.

---

## 29.3 Virtual Inheritance

**Virtual inheritance** solves this by ensuring only **one shared copy** of the common base class exists, no matter how many paths lead to it:

```cpp
class Animal {
public:
    std::string name;
};

class Flyable : virtual public Animal {};
class Swimmable : virtual public Animal {};

class Duck : public Flyable, public Swimmable {};

Duck d;
d.name = "Donald"; // no longer ambiguous — there's only one Animal now
```

With `virtual` inheritance, `Flyable` and `Swimmable` both share the same underlying `Animal` subobject, resolving the ambiguity from the diamond problem.

---

## 29.4 Best Practices And Alternatives

Multiple and virtual inheritance are powerful but can make class hierarchies hard to reason about — particularly around constructor initialization order and the added indirection virtual inheritance introduces. Some practical guidelines:

- Prefer **composition** ("has-a") over multiple inheritance when possible — giving `Duck` a `Wings` and a `Fins` member is often clearer than inheriting from `Flyable`/`Swimmable`.
- When multiple inheritance is the right tool, favor combining **interface-style** classes (pure virtual functions only, as in the previous lesson) — this avoids diamond problems entirely, since there's no shared data to duplicate.
- Reach for `virtual` inheritance only when you specifically need shared state across a diamond hierarchy — it adds real complexity and a small runtime cost, so it isn't a default choice.

This lesson concludes the **Object-Oriented Programming** topic group. The course continues next with **Generic Programming**, starting with function templates.

[Previous](./[28]-Abstract-Classes-and-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[30]-Function-Templates.md)
