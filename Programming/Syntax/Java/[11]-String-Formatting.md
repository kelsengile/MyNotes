[Previous](./[10]-Methods-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[12]-Arrays.md)

*Core Syntax*

# Lesson 11 - String Formatting & Manipulation (String, StringBuilder, text blocks)

Text processing is one of the most common tasks in programming. This lesson dives deeper into `String`, its mutable companion `StringBuilder`, and Java's formatting tools.

## 11.1 String Immutability

As mentioned in [Lesson 6](./[6]-Numbers-Strings-and-Booleans.md), `String` objects are **immutable** — every method that appears to modify a string actually returns a brand-new one:

```java
String original = "hello";
String upper = original.toUpperCase();
// original is still "hello"; upper is "HELLO"
```

This immutability makes strings safe to share across your program, but repeatedly building up a string in a loop (via `+=`) creates many throwaway objects — a performance concern addressed by `StringBuilder`.

---

## 11.2 Common String Methods

`String` provides a large set of built-in methods for inspecting and transforming text:

```java
String s = "Hello, World!";

s.length();            // 13
s.charAt(0);            // 'H'
s.substring(7, 12);     // "World"
s.indexOf("World");     // 7
s.toLowerCase();        // "hello, world!"
s.replace("World", "Java"); // "Hello, Java!"
s.trim();                // removes leading/trailing whitespace
s.split(", ");           // ["Hello", "World!"]
s.contains("World");     // true
```

---

## 11.3 StringBuilder

When you need to build or modify text repeatedly — such as inside a loop — use `StringBuilder`, which is **mutable** and far more efficient than repeated `String` concatenation:

```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 5; i++) {
    sb.append(i).append(", ");
}
String result = sb.toString(); // "0, 1, 2, 3, 4, "
```

`StringBuilder` also supports `insert()`, `delete()`, and `reverse()` for in-place modification.

---

## 11.4 String Formatting (String.format, printf)

For inserting values into a template string, use `String.format()` (or the equivalent `System.out.printf()`), which uses format specifiers like `%s` (string), `%d` (integer), and `%.2f` (decimal with 2 places):

```java
String message = String.format("Name: %s, Age: %d", "Alice", 30);
System.out.printf("Price: $%.2f%n", 19.999);
// Price: $20.00
```

This is preferable to manual concatenation whenever you're mixing text and formatted values.

---

## 11.5 Text Blocks

Since Java 15, **text blocks** let you write multi-line strings without messy escape characters, using triple double-quotes:

```java
String html = """
        <html>
            <body>
                <p>Hello, World!</p>
            </body>
        </html>
        """;
```

Text blocks automatically manage indentation and line breaks, making them ideal for embedding JSON, SQL, or HTML directly in your Java code.

---

[Previous](./[10]-Methods-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[12]-Arrays.md)