[Previous](./[17]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[19]-Encapsulation-and-Access-Modifiers.md)

*Object-Oriented Programming*

# Lesson 18 - Inheritance & Polymorphism

## 18.1 Inheritance Basics

**Inheritance** lets a class (the *derived* or *child* class) reuse the fields, properties, and methods of another class (the *base* or *parent* class), using a colon (`:`):

```csharp
public class Animal
{
    public string Name;

    public void Eat()
    {
        Console.WriteLine($"{Name} is eating.");
    }
}

public class Dog : Animal
{
    public void Fetch()
    {
        Console.WriteLine($"{Name} fetches the ball.");
    }
}

Dog myDog = new Dog { Name = "Rex" };
myDog.Eat();     // inherited from Animal
myDog.Fetch();   // defined on Dog
```

---

## 18.2 Overriding Methods

A derived class can provide its own implementation of a base class method using `virtual` (on the base) and `override` (on the derived class):

```csharp
public class Animal
{
    public virtual void MakeSound()
    {
        Console.WriteLine("Some generic animal sound");
    }
}

public class Dog : Animal
{
    public override void MakeSound()
    {
        Console.WriteLine("Woof!");
    }
}
```

Without `virtual` on the base method, `override` isn't allowed — the base method must explicitly opt in to being overridden.

---

## 18.3 Polymorphism

**Polymorphism** means that a variable of the base type can hold an object of any derived type, and calling an overridden method runs the *derived* class's version:

```csharp
Animal[] animals = { new Dog(), new Cat() };

foreach (Animal a in animals)
{
    a.MakeSound();   // calls Dog's or Cat's version, depending on the actual object
}
```

This is powerful because it lets you write code that works with the general `Animal` type while still getting each specific animal's own behavior.

---

## 18.4 The base Keyword

`base` lets a derived class call the base class's constructor or an overridden method's original implementation:

```csharp
public class Animal
{
    public string Name;
    public Animal(string name) { Name = name; }

    public virtual void MakeSound() => Console.WriteLine("...");
}

public class Dog : Animal
{
    public Dog(string name) : base(name) { }   // calls Animal's constructor

    public override void MakeSound()
    {
        base.MakeSound();          // runs Animal's version first
        Console.WriteLine("Woof!"); // then adds its own behavior
    }
}
```

[Previous](./[17]-OOP-Classes-and-Objects.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[19]-Encapsulation-and-Access-Modifiers.md)
