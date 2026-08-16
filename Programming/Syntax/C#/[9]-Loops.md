[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[10]-Methods-and-Parameters.md)

*Core Syntax*

# Lesson 9 - Loops: for, foreach, while, do-while, break, continue

## 9.1 for Loop

Use a `for` loop when you know how many times you want to repeat something:

```csharp
for (int i = 0; i < 5; i++)
{
    Console.WriteLine(i);   // prints 0 through 4
}
```

It has three parts: initialization (`int i = 0`), condition (`i < 5`), and increment (`i++`).

---

## 9.2 while and do-while

A `while` loop repeats as long as its condition is `true`, checked *before* each iteration:

```csharp
int count = 0;
while (count < 3)
{
    Console.WriteLine(count);
    count++;
}
```

A `do-while` loop checks the condition *after* each iteration, so the body always runs at least once:

```csharp
int input;
do
{
    input = GetUserInput();
} while (input != 0);
```

---

## 9.3 foreach

Use `foreach` to iterate over every item in a collection without managing an index manually:

```csharp
string[] fruits = { "apple", "banana", "cherry" };

foreach (string fruit in fruits)
{
    Console.WriteLine(fruit);
}
```

`foreach` works with arrays, `List<T>`, and anything implementing `IEnumerable<T>` (covered in [Lesson 15](./[15]-Collection-Interfaces.md)).

---

## 9.4 break and continue

`break` exits the loop immediately. `continue` skips the rest of the current iteration and moves to the next one:

```csharp
for (int i = 0; i < 10; i++)
{
    if (i == 5) break;       // stop the loop entirely at 5
    if (i % 2 == 0) continue; // skip even numbers
    Console.WriteLine(i);     // prints 1 and 3
}
```

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[10]-Methods-and-Parameters.md)
