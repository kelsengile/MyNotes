# Functions & Program Structure

Functions are reusable, named blocks of code that perform a specific task. They are the primary tool for organizing a program into manageable, testable, and reusable pieces, and they underpin how data and control flow through larger programs.

---

## 8.1 Defining and Calling Functions

A function is *defined* once and *called* (invoked) any number of times.

```python
# Python
def greet():
    print("Hello!")

greet()  # calling the function
```

```javascript
// JavaScript — function declaration
function greet() {
    console.log("Hello!");
}
greet();

// Function expression
const greet2 = function() {
    console.log("Hi!");
};

// Arrow function
const greet3 = () => {
    console.log("Hey!");
};
greet2();
greet3();
```

```c
// C
#include <stdio.h>

void greet() {
    printf("Hello!\n");
}

int main() {
    greet();
    return 0;
}
```

```java
// Java — functions are methods and must belong to a class
class Greeter {
    static void greet() {
        System.out.println("Hello!");
    }
}
Greeter.greet();
```

### 8.1.1 Function Declaration vs. Expression

- A **declaration** (`function greet() {}` in JS, `def greet():` in Python) creates a named function that can typically be referenced before its definition in the file (in JS, due to *hoisting*).
- An **expression** assigns a function to a variable (`const greet = function() {}`) and cannot be called before that line runs.

### 8.1.2 Anonymous and Arrow Functions

Functions don't always need a name — they can be passed around as values, especially for short, one-off operations.

```python
# Python lambda (anonymous function, single expression only)
square = lambda x: x * x
print(square(5))  # 25
```

```javascript
// JavaScript arrow function
const square = (x) => x * x;
console.log(square(5)); // 25
```

**Key points:**
- Every function has a name (or is anonymous), a parameter list, and a body.
- Calling a function transfers control to its body; execution resumes after the call once the function finishes (or returns).

---

## 8.2 Parameters, Return Values, Default Args

### 8.2.1 Parameters and Arguments

*Parameters* are the variables listed in a function's definition; *arguments* are the actual values passed in when calling it.

```python
def add(a, b):     # a, b are parameters
    return a + b

result = add(3, 4)  # 3, 4 are arguments
```

### 8.2.2 Return Values

A function can send a value back to the caller using `return`. Execution of the function stops immediately at `return`.

```python
def square(x):
    return x * x

print(square(4))  # 16
```

```javascript
function square(x) {
    return x * x;
}
```

If no explicit `return` is used, most languages return a default "empty" value — `None` in Python, `undefined` in JavaScript, `void` in Java/C (no value at all).

### 8.2.3 Multiple Return Values

Some languages allow returning multiple values, often via a tuple or similar structure.

```python
def min_max(numbers):
    return min(numbers), max(numbers)

lo, hi = min_max([4, 1, 9, 2])
print(lo, hi)  # 1 9
```

```go
// Go supports multiple return values natively
func divide(a, b int) (int, int) {
    return a / b, a % b
}
quotient, remainder := divide(17, 5)
```

### 8.2.4 Default Arguments

Default arguments let a parameter fall back to a preset value if the caller doesn't supply one.

```python
def greet(name="World"):
    print(f"Hello, {name}!")

greet()          # Hello, World!
greet("Alice")   # Hello, Alice!
```

```javascript
function greet(name = "World") {
    console.log(`Hello, ${name}!`);
}
```

### 8.2.5 Keyword Arguments and Named Parameters

Some languages let arguments be passed by name rather than position, improving readability for functions with many parameters.

```python
def describe(name, age, city="Unknown"):
    print(f"{name}, {age}, from {city}")

describe(name="Alice", city="Paris", age=30)  # order doesn't matter
```

### 8.2.6 Variadic Parameters (Variable Number of Arguments)

Functions can accept an arbitrary number of arguments.

```python
def total(*numbers):     # collects extra positional args into a tuple
    return sum(numbers)

print(total(1, 2, 3, 4))  # 10
```

```javascript
function total(...numbers) {   // rest parameter
    return numbers.reduce((sum, n) => sum + n, 0);
}
console.log(total(1, 2, 3, 4)); // 10
```

### 8.2.7 Pass by Value vs. Pass by Reference

How arguments are passed affects whether changes inside a function are visible to the caller.

- **Pass by value:** a copy of the value is passed; modifying the parameter inside the function does not affect the original (common for primitives like numbers/booleans in most languages).
- **Pass by reference (or "by object reference"):** a reference to the same underlying object is passed; mutating the object's contents *is* visible outside the function, though reassigning the parameter itself is not.

```python
def modify_list(lst):
    lst.append(4)      # mutates the original list

def reassign_list(lst):
    lst = [9, 9, 9]     # only rebinds the local variable; caller unaffected

nums = [1, 2, 3]
modify_list(nums)
print(nums)  # [1, 2, 3, 4]

reassign_list(nums)
print(nums)  # [1, 2, 3, 4]  (unchanged)
```

---

## 8.3 Scope and Lifetime of Variables

**Scope** determines *where* in a program a variable name is visible/accessible. **Lifetime** determines *how long* a variable exists in memory before it's cleaned up.

### 8.3.1 Local Scope

Variables declared inside a function are typically only accessible within that function.

```python
def my_function():
    x = 10   # local to my_function
    print(x)

my_function()
print(x)  # NameError: x is not defined
```

### 8.3.2 Global Scope

Variables declared outside any function are accessible throughout the file (and often need special syntax to *modify* from within a function).

```python
count = 0  # global

def increment():
    global count   # without this, count += 1 would raise an error
    count += 1

increment()
print(count)  # 1
```

```javascript
let count = 0;

function increment() {
    count++;  // JavaScript allows modifying outer-scope variables directly
}
increment();
console.log(count); // 1
```

### 8.3.3 Block Scope vs. Function Scope

- **Function-scoped** variables (e.g., `var` in JavaScript, variables in Python) are visible throughout the entire function they're declared in, regardless of nested blocks.
- **Block-scoped** variables (e.g., `let`/`const` in JavaScript, variables in C/Java/Rust) are only visible within the nearest enclosing `{}` block (like an `if` or `for` body).

```javascript
if (true) {
    var a = 1;    // function-scoped — leaks outside the if-block
    let b = 2;    // block-scoped — confined to this block
}
console.log(a);  // 1
console.log(b);  // ReferenceError
```

> Note: Python does not have block scope — variables defined inside an `if` or `for` block are accessible in the enclosing function.

### 8.3.4 Nested/Lexical Scope

Inner functions can access variables from their enclosing (outer) function's scope. This chain of accessible scopes is called the **scope chain**, and lookup typically resolves from innermost to outermost.

```python
def outer():
    message = "Hi"
    def inner():
        print(message)  # inner can see outer's variable
    inner()

outer()
```

### 8.3.5 Variable Lifetime

- **Local variables** typically live only as long as the function call is executing — they are created when the function is called and destroyed when it returns (unless captured by a closure — see 8.5).
- **Global variables** live for the duration of the entire program.
- In lower-level languages (like C), variables can also be allocated on the **stack** (automatic, short lifetime tied to function calls) or the **heap** (manual or garbage-collected, can outlive the function that created them).

### 8.3.6 Shadowing

A variable declared in an inner scope with the same name as one in an outer scope "shadows" (hides) the outer one within that inner scope.

```python
x = "outer"

def my_func():
    x = "inner"  # shadows the outer x within this function
    print(x)     # inner

my_func()
print(x)  # outer (unaffected)
```

---

## 8.4 Recursion

Recursion occurs when a function calls itself, directly or indirectly, to solve a problem by breaking it into smaller instances of the same problem.

### 8.4.1 Anatomy of a Recursive Function

Every correct recursive function needs:
1. **Base case(s):** a condition where the function returns a result directly, without recursing — this stops infinite recursion.
2. **Recursive case:** the function calls itself with a smaller/simpler input, making progress toward the base case.

```python
def factorial(n):
    if n <= 1:          # base case
        return 1
    return n * factorial(n - 1)  # recursive case

print(factorial(5))  # 120
```

```javascript
function factorial(n) {
    if (n <= 1) return 1;        // base case
    return n * factorial(n - 1); // recursive case
}
```

### 8.4.2 How Recursion Works: The Call Stack

Each recursive call adds a new **stack frame** to the program's call stack, holding that call's local variables and the point to return to. When a call returns, its frame is popped off, and execution resumes in the caller.

```
factorial(3)
  -> factorial(2)
       -> factorial(1) -> returns 1
     <- returns 2 * 1 = 2
<- returns 3 * 2 = 6
```

If recursion never reaches a base case, the call stack grows until it exhausts available memory — a **stack overflow**.

### 8.4.3 Classic Examples

```python
# Fibonacci sequence
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)

# Sum of a list
def sum_list(lst):
    if not lst:
        return 0
    return lst[0] + sum_list(lst[1:])

# Traversing a nested/tree structure
def count_nodes(tree):
    if tree is None:
        return 0
    return 1 + sum(count_nodes(child) for child in tree.children)
```

### 8.4.4 Recursion vs. Iteration

Any recursive solution can, in principle, be rewritten iteratively (using loops and an explicit stack) and vice versa.

| Aspect                | Recursion                              | Iteration                        |
|--------------------------|-------------------------------------------|--------------------------------------|
| Readability                | Often clearer for naturally recursive problems (trees, divide-and-conquer) | Often clearer for simple repetition |
| Memory                       | Uses call stack — can be costly for deep recursion | Generally constant extra memory   |
| Risk                            | Stack overflow if base case is missed/unreachable | Infinite loop if exit condition is wrong |

### 8.4.5 Tail Recursion

A recursive call is in **tail position** if it's the very last action in the function (nothing happens after it returns). Some languages/compilers optimize tail calls into loops (**tail call optimization**), avoiding extra stack frames — though this optimization is not guaranteed in all languages (notably, standard Python does not perform it).

```python
# Tail-recursive style (Python won't optimize this automatically)
def factorial_tail(n, accumulator=1):
    if n <= 1:
        return accumulator
    return factorial_tail(n - 1, n * accumulator)  # last action is the recursive call
```

---

## 8.5 Higher-Order Functions & Closures

### 8.5.1 Functions as First-Class Values

In many modern languages, functions are **first-class citizens** — they can be assigned to variables, stored in data structures, and passed around just like any other value.

```python
def shout(text):
    return text.upper()

my_func = shout          # assign function to a variable
print(my_func("hello"))  # HELLO
```

### 8.5.2 Higher-Order Functions

A **higher-order function** either takes another function as an argument, returns a function, or both.

```python
# Takes a function as an argument
def apply_twice(func, value):
    return func(func(value))

print(apply_twice(lambda x: x + 3, 10))  # 16

# Common built-in higher-order functions
numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, numbers))       # [2, 4, 6, 8, 10]
evens = list(filter(lambda x: x % 2 == 0, numbers))  # [2, 4]
from functools import reduce
total = reduce(lambda a, b: a + b, numbers)          # 15
```

```javascript
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(x => x * 2);
const evens = numbers.filter(x => x % 2 === 0);
const total = numbers.reduce((a, b) => a + b, 0);
```

### 8.5.3 Functions Returning Functions

```python
def make_multiplier(factor):
    def multiplier(x):
        return x * factor
    return multiplier   # returns a function

double = make_multiplier(2)
triple = make_multiplier(3)
print(double(5))  # 10
print(triple(5))  # 15
```

### 8.5.4 Closures

A **closure** is formed when an inner function "remembers" and can still access variables from its enclosing scope, even after the outer function has finished executing. In `make_multiplier` above, `multiplier` is a closure — it retains access to `factor` from `make_multiplier`'s scope.

```javascript
function makeCounter() {
    let count = 0;          // captured by the closure
    return function () {
        count++;
        return count;
    };
}

const counter = makeCounter();
console.log(counter()); // 1
console.log(counter()); // 2
console.log(counter()); // 3 — count persists between calls
```

**Key points:**
- Each call to the outer function creates a *new*, independent closure with its own captured variables.
- Closures are commonly used for data encapsulation (private state), callbacks, event handlers, and factory functions.
- Because the captured variable stays alive as long as the closure exists, closures can extend a variable's lifetime beyond its normal scope — worth keeping in mind for memory usage in long-lived closures.

---

## 8.6 Pure Functions & Side Effects

### 8.6.1 Pure Functions

A function is **pure** if it satisfies two conditions:
1. **Deterministic:** given the same inputs, it always produces the same output.
2. **No side effects:** it does not modify anything outside its own scope (no mutating external variables, no I/O, no changing its arguments).

```python
# Pure
def add(a, b):
    return a + b

# Pure
def square_all(numbers):
    return [n * n for n in numbers]  # returns a new list, doesn't mutate input
```

### 8.6.2 Side Effects

A **side effect** is any observable interaction with the world outside a function's own local scope. Examples include:
- Modifying a global variable or an object/array passed by reference
- Printing to the console or writing to a file
- Making a network request or database call
- Changing the DOM
- Reading from a random number generator or the system clock

```python
total = 0

def add_to_total(x):   # impure: mutates external state
    global total
    total += x

def log_and_add(a, b):  # impure: has a side effect (printing)
    print(f"Adding {a} and {b}")
    return a + b
```

### 8.6.3 Why Purity Matters

| Benefit                         | Explanation                                                              |
|-------------------------------------|-------------------------------------------------------------------------|
| Predictability                      | Same inputs always give the same output — no hidden state to track     |
| Easier testing                       | No setup/teardown of external state; just check input → output         |
| Easier reasoning & debugging          | A pure function's behavior is fully described by its signature       |
| Safe to run in parallel                | No shared mutable state means no race conditions between calls      |
| Memoizable/cacheable                    | Results can be cached since the same input always yields the same output |

### 8.6.4 Impurity Is Not "Wrong"

Real programs need side effects to be useful — printing output, saving files, updating a UI, or calling an API are all side effects, and a program with *zero* side effects couldn't interact with the outside world at all. The practical goal in most codebases isn't eliminating side effects entirely, but **isolating** them: keeping core logic in small, pure, easily testable functions, and pushing side effects (I/O, mutation, randomness) to the edges of the program.

```python
# Pure core logic
def calculate_total(cart_items):
    return sum(item["price"] * item["quantity"] for item in cart_items)

# Side effect isolated to a thin wrapper
def checkout(cart_items):
    total = calculate_total(cart_items)   # pure computation
    print(f"Total: ${total:.2f}")          # side effect, kept separate
    return total
```

### 8.6.5 Referential Transparency

An expression is **referentially transparent** if it can be replaced with its resulting value without changing the program's behavior. Pure function calls have this property — `add(2, 3)` can always be replaced with `5` — which is part of what makes pure functions easy to reason about and optimize.