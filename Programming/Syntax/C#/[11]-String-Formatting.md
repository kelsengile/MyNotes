[Previous](./[10]-Methods-and-Parameters.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[12]-Exception-Handling.md)

*Core Syntax*

# Lesson 11 - String Formatting & Manipulation (interpolation, `string` methods)

## 11.1 String Interpolation

Interpolated strings, prefixed with `$`, let you embed expressions directly inside a string:

```csharp
string name = "Ava";
int age = 25;
Console.WriteLine($"{name} is {age} years old.");
// "Ava is 25 years old."
```

You can also apply format specifiers, for example to control decimal places or currency:

```csharp
double price = 19.999;
Console.WriteLine($"{price:F2}");   // "20.00"
Console.WriteLine($"{price:C}");    // "$20.00" (culture-dependent)
```

---

## 11.2 Common String Methods

```csharp
string text = "  Hello, World!  ";

text.Trim();                 // "Hello, World!"  (removes leading/trailing whitespace)
text.ToUpper();               // "  HELLO, WORLD!  "
text.ToLower();               // "  hello, world!  "
text.Contains("World");       // true
text.Replace("World", "C#");  // "  Hello, C#!  "
text.Split(',');              // ["  Hello", " World!  "]
text.Substring(2, 5);         // "Hello"
string.IsNullOrEmpty(text);   // false
```

Remember: since strings are immutable, every one of these returns a *new* string rather than modifying `text` in place.

---

## 11.3 StringBuilder

Repeatedly concatenating strings in a loop (`result += ...`) creates a new string object every time, which is wasteful. `StringBuilder` mutates an internal buffer instead, making it far more efficient for building up text incrementally:

```csharp
using System.Text;

var sb = new StringBuilder();
for (int i = 0; i < 5; i++)
{
    sb.Append(i).Append(", ");
}
string result = sb.ToString();   // "0, 1, 2, 3, 4, "
```

[Previous](./[10]-Methods-and-Parameters.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[12]-Exception-Handling.md)
