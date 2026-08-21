[Previous](./[5]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[7]-Classes-and-Objects.md)

*Core Syntax*

# Lesson 6 - Functions & Methods

## 6.1 Defining Functions

A function is a reusable, named block of code. A **method** is simply a function that belongs to a class or object (covered in the next lesson).

```dart
int addTax(double price, double taxRate) {
  return (price * (1 + taxRate)).round();
}
```

```swift
func addTax(price: Double, taxRate: Double) -> Int {
    return Int((price * (1 + taxRate)).rounded())
}
```

Mobile languages typically require you to declare a return type, which the compiler checks — calling `addTax()` and expecting a `String` back would fail to compile, catching the bug before the app ever runs.

---

## 6.2 Named and Default Parameters

Unlike many general-purpose languages, mobile UI languages heavily use **named parameters** — you specify which value goes with which parameter, in any order, which makes long function calls (especially ones that build UI, as in Lesson 10) far more readable:

```dart
Text("Hello", style: TextStyle(fontSize: 20), textAlign: TextAlign.center);
```

**Default parameter values** let a function work sensibly even when the caller omits some arguments:

```kotlin
fun greet(name: String = "Guest") = "Hello, $name!"
greet()         // "Hello, Guest!"
greet("Sam")    // "Hello, Sam!"
```

---

## 6.3 Closures / Lambdas / Callbacks

A **closure** (Swift), **lambda** (Kotlin), or **anonymous function** (Dart) is a function without a name that can be passed around as a value — essential in mobile UI, where you constantly need to say "run this code when the button is tapped":

```dart
ElevatedButton(
  onPressed: () {
    print("Button tapped!");
  },
  child: Text("Tap me"),
);
```

This callback pattern shows up everywhere: button taps, network responses, list item selection, and animation completion.

---

## 6.4 Async Functions (Preview)

Many mobile functions need to wait for something slow, like a network response, without freezing the UI. Languages mark these with keywords like `async`/`await` — this is explored fully in Lesson 21, but it's worth recognizing the syntax now since you'll see it throughout the course:

```dart
Future<String> fetchUsername() async {
  final response = await http.get(url);
  return response.body;
}
```

[Previous](./[5]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[7]-Classes-and-Objects.md)
