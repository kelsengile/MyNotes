[Previous](./[11]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[13]-Lists.md)

*Core Syntax*

# Lesson 12 - Functions

A **function** is a named, reusable block of code. Functions let you break a big problem into small pieces, avoid repeating yourself, and give meaningful names to ideas. In Dart, functions are also *objects*, which unlocks powerful techniques such as passing a function to another function.

---

## 12.1 Declaring and Calling Functions

A function declaration has a **return type**, a **name**, a **parameter list**, and a **body**:

```dart
int add(int a, int b) {
  return a + b;
}

void main() {
  int result = add(3, 4);   // calling the function
  print(result);            // 7
}
```

- `int` before the name is the **return type**. The `return` statement sends a value back to the caller.
- `a` and `b` are **parameters**; the values you pass when calling (`3` and `4`) are called **arguments**.
- Functions that do not return a value use the return type `void`.

```dart
void greet(String name) {
  print('Hello, $name!');
}
```

A few facts:

- Functions can be declared at the **top level** (outside any class) or **inside other functions**.
- Dart does **not** support function overloading: you cannot have two functions with the same name and different parameters. Use optional or named parameters instead.
- You can omit the return type, and Dart will infer `dynamic`, but the style guide asks you to write it.
- A function without a `return` statement implicitly returns `null` (for `void` functions this value cannot be used).

```dart
void main() {
  double average(List<int> values) {   // a function inside main()
    var total = 0;
    for (final v in values) {
      total += v;
    }
    return total / values.length;
  }

  print(average([4, 8, 12]));  // 8.0
}
```

---

## 12.2 Positional, Named, and Optional Parameters

Dart offers three styles of parameters.

### Required positional parameters

The caller must supply them **in order**:

```dart
String fullName(String first, String last) => '$first $last';

void main() {
  print(fullName('Ada', 'Lovelace'));  // Ada Lovelace
}
```

### Optional positional parameters: `[ ]`

Wrapping parameters in square brackets makes them optional. If omitted, they are `null` unless they have a default value, so their type must be nullable (or have a default):

```dart
String describe(String name, [int? age]) {
  if (age == null) return name;
  return '$name ($age)';
}

void main() {
  print(describe('Ana'));      // Ana
  print(describe('Ana', 21));  // Ana (21)
}
```

### Named parameters: `{ }`

Wrapping parameters in curly braces makes them **named**. The caller writes the name, so call sites are self-explanatory and the order does not matter:

```dart
void createUser({String? name, int? age, bool isAdmin = false}) {
  print('name=$name age=$age admin=$isAdmin');
}

void main() {
  createUser(name: 'Ana', age: 21);        // name=Ana age=21 admin=false
  createUser(isAdmin: true, name: 'Ben');  // name=Ben age=null admin=true
  createUser();                            // name=null age=null admin=false
}
```

### Mixing them

You can combine required positional parameters with **either** optional positional **or** named parameters, but not both optional kinds in one function. Required positionals always come first:

```dart
void log(String message, {int level = 0, bool timestamp = false}) {
  print('${timestamp ? '[now] ' : ''}[L$level] $message');
}

void main() {
  log('Started');                           // [L0] Started
  log('Disk full', level: 2);               // [L2] Disk full
  log('Done', timestamp: true, level: 1);   // [now] [L1] Done
}
```

**Which should you use?** Prefer **named** parameters when a function has more than two or three parameters or when some are boolean flags. Compare `createUser('Ana', 21, true)` (what does `true` mean?) with `createUser(name: 'Ana', age: 21, isAdmin: true)`.

---

## 12.3 `required` and Default Values

### `required`

A named parameter is optional by default. Mark it `required` to force the caller to provide it. A required named parameter can have a non-nullable type with no default:

```dart
void signUp({required String email, required String password, String? phone}) {
  print('Signing up $email (phone: $phone)');
}

void main() {
  signUp(email: 'ana@example.com', password: 'secret');
  // signUp(email: 'ana@example.com');  // ERROR: 'password' is required
}
```

### Default values

Give an optional parameter a **default value** with `=`. The default must be a **compile-time constant**:

```dart
double applyDiscount(double price, {double percent = 10}) {
  return price - price * percent / 100;
}

String repeat(String text, [int times = 2]) => text * times;

void main() {
  print(applyDiscount(200));                  // 180.0
  print(applyDiscount(200, percent: 25));     // 150.0
  print(repeat('ha'));                        // haha
  print(repeat('ha', 3));                     // hahaha
}
```

Rules to remember:

| Parameter kind | If not provided | Allowed type |
|---|---|---|
| Positional `[int x]` | Compile error unless it has a default or is nullable | Nullable or has default |
| Named `{int x}` | Same | Nullable or has default |
| `required` named | **Must** be provided | Any |

A non-nullable optional parameter **must** have a default value, otherwise the analyzer reports an error:

```dart
// void f({int count}) {}      // ERROR: non-nullable without default
void f({int count = 0}) {}     // OK
void g({int? count}) {}        // OK
void h({required int count}) {} // OK
```

---

## 12.4 Arrow Syntax (`=>`)

When a function body is a **single expression**, you can replace `{ return ...; }` with `=> expression;`:

```dart
int square(int n) {
  return n * n;
}

int squareShort(int n) => n * n;   // same thing, shorter
```

The expression's value is automatically returned. Arrow syntax works for any function, including those that return `void`:

```dart
void sayHi() => print('Hi!');

bool isAdult(int age) => age >= 18;

String label(int n) => n == 1 ? 'item' : 'items';
```

Limitations:

- Only a **single expression** is allowed; you cannot put statements like `if` or a `for` loop after `=>`. (A conditional *expression* `a ? b : c` is allowed, and so are `switch` expressions.)
- There is no semicolon-separated body; the arrow form ends with a single `;`.

Use arrows for short, simple functions and braces for anything longer. Readability matters most.

---

## 12.5 Functions as First-Class Objects

In Dart, every function is an object of type `Function`. That means you can:

- store a function in a variable,
- pass a function as an argument,
- return a function from another function.

### Storing in a variable

```dart
int add(int a, int b) => a + b;

void main() {
  int Function(int, int) op = add;   // a variable holding a function
  print(op(2, 3));                    // 5

  var multiply = (int a, int b) => a * b;
  print(multiply(2, 3));              // 6
}
```

The type `int Function(int, int)` reads: "a function that takes two `int`s and returns an `int`".

### Passing to another function

A function that accepts another function is called a **higher-order function**:

```dart
int applyTwice(int Function(int) f, int value) => f(f(value));

int addTen(int n) => n + 10;

void main() {
  print(applyTwice(addTen, 1));        // 21
  print(applyTwice((n) => n * 3, 2));  // 18
}
```

### Returning a function

```dart
Function(int) makeAdder(int amount) {
  return (int n) => n + amount;
}

void main() {
  var addFive = makeAdder(5);
  print(addFive(10));   // 15
}
```

This is exactly how `list.map(...)`, `list.where(...)`, and `list.sort(...)` work: you give them a function describing *what* to do, and they handle the looping.

---

## 12.6 Anonymous Functions and Closures

### Anonymous functions

A function without a name is an **anonymous function** (also called a lambda). You write just the parameters and a body:

```dart
void main() {
  var numbers = [1, 2, 3];

  numbers.forEach((n) {          // block body
    print(n * 2);
  });

  var doubled = numbers.map((n) => n * 2);  // arrow body
  print(doubled.toList());       // [2, 4, 6]
}
```

Parameter types are usually inferred from context, so you can omit them.

### Closures

A **closure** is a function that **remembers the variables around it**, even after the surrounding function has finished:

```dart
Function() makeCounter() {
  var count = 0;
  return () {
    count++;
    return count;
  };
}

void main() {
  var a = makeCounter();
  var b = makeCounter();

  print(a());   // 1
  print(a());   // 2
  print(a());   // 3
  print(b());   // 1  (b has its own separate count)
}
```

Each call to `makeCounter()` creates a new `count`, and the returned function keeps access to it. Closures are useful for callbacks, event handlers, and keeping private state without a class.

---

## 12.7 Lexical Scope

Dart uses **lexical scope**: where a variable can be used is decided by where it is written in the code, using the nesting of curly braces. An inner scope can see variables from outer scopes, but not the other way around.

```dart
var topLevel = 'visible everywhere in this file';

void main() {
  var insideMain = 'visible in main and inner blocks';

  void inner() {
    var insideInner = 'visible only in inner';
    print(topLevel);      // OK
    print(insideMain);    // OK
    print(insideInner);   // OK
  }

  inner();
  print(topLevel);        // OK
  print(insideMain);      // OK
  // print(insideInner);  // ERROR: undefined name, out of scope
}
```

A variable declared inside a block `{ }` only exists in that block:

```dart
void main() {
  if (true) {
    var temp = 5;
    print(temp);   // OK
  }
  // print(temp);  // ERROR: 'temp' is not defined here
}
```

If an inner scope declares a variable with the same name as an outer one, the inner one **shadows** the outer one inside that scope. This is legal but can confuse readers, so avoid it.

---

## 12.8 Recursion

A **recursive** function calls itself. It needs a **base case** that stops the recursion; otherwise it runs until the program crashes with a stack overflow.

```dart
int factorial(int n) {
  if (n <= 1) return 1;          // base case
  return n * factorial(n - 1);   // recursive case
}

void main() {
  print(factorial(5));  // 120  (5 * 4 * 3 * 2 * 1)
}
```

Trace of `factorial(3)`:

```
factorial(3) = 3 * factorial(2)
             = 3 * (2 * factorial(1))
             = 3 * (2 * 1)
             = 6
```

Another classic example, the Fibonacci sequence (each number is the sum of the previous two):

```dart
int fib(int n) => n < 2 ? n : fib(n - 1) + fib(n - 2);

void main() {
  print([for (var i = 0; i < 10; i++) fib(i)]);
  // [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
}
```

Notes:

- Any recursion can be rewritten as a loop. Loops are usually faster and cannot overflow the stack.
- Dart does **not** optimize tail calls, so very deep recursion (tens of thousands of levels) can fail.
- The naive `fib` above recomputes the same values many times and becomes very slow for large `n`. Recursion shines for naturally recursive structures such as trees and nested folders.

---

## 12.9 Tear-offs

When you write a function's name **without parentheses**, you get the function itself, as an object. This is a **tear-off**. It lets you pass an existing function directly instead of wrapping it in an anonymous function.

```dart
void main() {
  var names = ['Ana', 'Ben'];

  names.forEach((n) => print(n));  // wrapper: unnecessary
  names.forEach(print);            // tear-off: same result, cleaner
}
```

More examples:

```dart
void main() {
  var numbers = ['1', '2', '3'].map(int.parse).toList();
  print(numbers);   // [1, 2, 3]

  var upper = ['a', 'b'].map((s) => s.toUpperCase()); // arguments differ: use lambda
  print(upper.toList());
}

bool isPositive(int n) => n > 0;

void demo() {
  var all = [3, -1, 7].where(isPositive).toList();
  print(all);       // [3, 7]
}
```

Method tear-offs keep their object:

```dart
void main() {
  var text = 'hello';
  var shout = text.toUpperCase;   // tear-off of a method (no parentheses)
  print(shout());                 // HELLO
}
```

Constructors can be torn off too, using `.new`:

```dart
void main() {
  var makeList = List<int>.new;       // tear-off of a constructor
  var buffers = [1, 2].map((_) => StringBuffer.new()).toList();
  print(makeList());                  // []
  print(buffers.length);              // 2
}
```

The rule of thumb: if a lambda does nothing except call another function with the same arguments, replace it with a tear-off (the `unnecessary_lambdas` lint rule checks for this).

---

[Previous](./[11]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Dart.md) | [Next](./[13]-Lists.md)
