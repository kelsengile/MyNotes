[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Methods & Parameters (ref, out, params, overloading)

## 10.1 Defining Methods

A method is a named, reusable block of code:

```csharp
static int Add(int a, int b)
{
    return a + b;
}

int result = Add(3, 4);   // 7
```

The `static` keyword here means the method belongs to the class itself rather than to an instance of it — more on that in [Lesson 17](./[17]-OOP-Classes-and-Objects.md).

---

## 10.2 Parameters and Return Values

Parameters are the inputs a method accepts; the return type declares what it hands back. A method that returns nothing uses `void`:

```csharp
static void Greet(string name)
{
    Console.WriteLine($"Hello, {name}!");
}
```

Parameters can have default values, making them optional when calling the method:

```csharp
static void Greet(string name = "Guest")
{
    Console.WriteLine($"Hello, {name}!");
}

Greet();          // "Hello, Guest!"
Greet("Ava");      // "Hello, Ava!"
```

---

## 10.3 ref, out, and params

By default, arguments are passed **by value** — the method gets a copy. These keywords change that behavior:

- **`ref`** passes a variable by reference, so changes inside the method affect the original. The variable must be initialized before the call.
- **`out`** also passes by reference, but is meant for methods that *produce* a value the caller doesn't have yet — no initial value is required.
- **`params`** lets a method accept a variable number of arguments as an array.

```csharp
static void Double(ref int n) => n *= 2;

int x = 5;
Double(ref x);   // x is now 10

static bool TryDivide(int a, int b, out int result)
{
    if (b == 0) { result = 0; return false; }
    result = a / b;
    return true;
}

static int Sum(params int[] numbers)
{
    int total = 0;
    foreach (int n in numbers) total += n;
    return total;
}

Sum(1, 2, 3, 4);   // 10
```

---

## 10.4 Method Overloading

**Overloading** means defining multiple methods with the same name but different parameter lists. The compiler picks the right one based on the arguments you pass:

```csharp
static int Add(int a, int b) => a + b;
static double Add(double a, double b) => a + b;
static int Add(int a, int b, int c) => a + b + c;
```

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[11]-String-Formatting.md)
