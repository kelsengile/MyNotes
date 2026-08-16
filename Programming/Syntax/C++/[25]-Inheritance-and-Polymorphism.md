[Previous](./[24]-Rule-of-Three-Five-Zero.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[26]-Encapsulation-and-Access-Control.md)

*Object-Oriented Programming*

# Lesson 25 - Inheritance And Polymorphism

## 25.1 Basic Inheritance

Inheritance lets one class (the **derived** class) reuse and extend the members of another (the **base** class):

```cpp
class Animal {
public:
    std::string name;

    void eat() {
        std::cout << name << " is eating\n";
    }
};

class Dog : public Animal { // Dog inherits from Animal
public:
    void bark() {
        std::cout << name << " says woof!\n"; // uses inherited member
    }
};

Dog d;
d.name = "Rex"; // inherited from Animal
d.eat();         // inherited from Animal
d.bark();        // defined in Dog
```

`public` inheritance means "a `Dog` **is an** `Animal`" — the most common and intuitive form of inheritance.

---

## 25.2 Overriding Behavior With virtual

Marking a base class function `virtual` allows a derived class to **override** it with its own implementation:

```cpp
class Animal {
public:
    virtual void makeSound() {
        std::cout << "Some generic animal sound\n";
    }
};

class Cat : public Animal {
public:
    void makeSound() override { // 'override' catches typos at compile time
        std::cout << "Meow\n";
    }
};

Animal* a = new Cat();
a->makeSound(); // prints "Meow" — the Cat version, even through an Animal pointer
delete a;
```

Without `virtual`, calling `makeSound()` through an `Animal*` would always run `Animal`'s version, regardless of the actual object's type — this is the core distinction that makes polymorphism possible.

---

## 25.3 How vtables Work (Conceptually)

When a class has `virtual` functions, the compiler generates a hidden **virtual table (vtable)** — essentially an array of function pointers, one per virtual function. Each object of that class stores a hidden pointer to its class's vtable. When you call a virtual function through a base pointer or reference, the program looks up the correct function via the vtable **at runtime**, based on the object's actual type — not the pointer's declared type. This is called **dynamic dispatch**, and it's what makes `a->makeSound()` above correctly call `Cat::makeSound()`, even though `a` is declared as `Animal*`.

Non-virtual function calls, by contrast, are resolved entirely at compile time based on the pointer's declared type — this is why `virtual` is necessary for polymorphism to work at all.

---

## 25.4 Polymorphism In Practice

Polymorphism shines when working with collections of different derived types through a common base pointer or reference:

```cpp
std::vector<std::unique_ptr<Animal>> animals;
animals.push_back(std::make_unique<Dog>());
animals.push_back(std::make_unique<Cat>());

for (const auto& animal : animals) {
    animal->makeSound(); // calls the correct override for each actual type
}
```

This lets code written against the base class (`Animal`) work correctly with any derived type, including ones written later — a central idea in flexible, extensible object-oriented design.

[Previous](./[24]-Rule-of-Three-Five-Zero.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[26]-Encapsulation-and-Access-Control.md)
