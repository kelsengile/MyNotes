[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[9]-Loops.md)

*Core Syntax*

# Lesson 8 - Conditionals: if, else, switch expressions

## 8.1 if / else

```csharp
int age = 20;

if (age >= 18)
{
    Console.WriteLine("Adult");
}
else
{
    Console.WriteLine("Minor");
}
```

The code inside `{ }` runs only when the condition evaluates to `true`.

---

## 8.2 else if Chains

Chain multiple conditions with `else if` when you have more than two branches:

```csharp
int score = 75;

if (score >= 90)
{
    Console.WriteLine("A");
}
else if (score >= 80)
{
    Console.WriteLine("B");
}
else if (score >= 70)
{
    Console.WriteLine("C");
}
else
{
    Console.WriteLine("F");
}
```

Conditions are checked top to bottom, and only the first matching branch runs.

---

## 8.3 switch Statements

A `switch` statement is a clean alternative to a long `else if` chain when checking one value against several possibilities:

```csharp
int day = 3;

switch (day)
{
    case 1:
        Console.WriteLine("Monday");
        break;
    case 2:
        Console.WriteLine("Tuesday");
        break;
    case 3:
        Console.WriteLine("Wednesday");
        break;
    default:
        Console.WriteLine("Unknown");
        break;
}
```

Each `case` needs a `break` (or `return`) so execution doesn't fall through to the next case.

---

## 8.4 switch Expressions

A **switch expression** is a more concise, modern form that directly produces a value:

```csharp
string dayName = day switch
{
    1 => "Monday",
    2 => "Tuesday",
    3 => "Wednesday",
    _ => "Unknown"   // _ is the default case
};
```

Switch expressions are preferred when every branch simply maps an input to an output value.

[Previous](./[7]-Operators-and-Expressions.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[9]-Loops.md)
