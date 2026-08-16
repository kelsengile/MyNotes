[Previous](./[16]-Tuples-and-Records.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[18]-Inheritance-and-Polymorphism.md)

*Object-Oriented Programming*

# Lesson 17 - Classes & Objects

## 17.1 Defining a Class

A **class** is a blueprint describing the data (fields/properties) and behavior (methods) that its objects will have:

```csharp
public class Dog
{
    public string Name;
    public int Age;

    public void Bark()
    {
        Console.WriteLine($"{Name} says Woof!");
    }
}
```

---

## 17.2 Creating Objects

An **object** (or instance) is a concrete thing created from a class using `new`:

```csharp
Dog myDog = new Dog();
myDog.Name = "Rex";
myDog.Age = 3;
myDog.Bark();   // "Rex says Woof!"

Dog anotherDog = new Dog();
anotherDog.Name = "Fido";
```

Each object has its own independent copy of the class's fields — changing `myDog.Name` doesn't affect `anotherDog`.

---

## 17.3 Constructors

A **constructor** is a special method that runs when an object is created, typically used to set up initial values:

```csharp
public class Dog
{
    public string Name;
    public int Age;

    public Dog(string name, int age)
    {
        Name = name;
        Age = age;
    }
}

Dog myDog = new Dog("Rex", 3);   // fields are set immediately
```

If you don't define any constructor, C# provides a default, parameterless one automatically — but that disappears once you define your own.

---

## 17.4 Fields vs Properties

A **field** is a plain variable declared directly in a class. A **property** wraps access to a value with `get`/`set` accessors, which is the more common and idiomatic approach in modern C# because it lets you add validation or logic later without changing how callers use it:

```csharp
public class Dog
{
    private string _name;

    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }

    // shorthand for the same thing:
    public int Age { get; set; }
}
```

Properties are covered in more depth in [Lesson 21](./[21]-Properties-and-Indexers.md), and access control (`public`/`private`) in [Lesson 19](./[19]-Encapsulation-and-Access-Modifiers.md).

[Previous](./[16]-Tuples-and-Records.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[18]-Inheritance-and-Polymorphism.md)
