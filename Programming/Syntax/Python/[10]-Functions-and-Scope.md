[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[11]-String-Formatting.md)

*Core Syntax*

# Lesson 10 - Functions & Scope

## 10.1 Defining and Calling Functions

A function is a reusable, named block of code. Define one with `def`, and call it by name followed by parentheses:

```python
def greet():
    print("Hello!")

greet()  # calls the function → prints "Hello!"
```

---

## 10.2 Parameters, Arguments & Return Values

Functions can accept input (**parameters**, defined in the function signature; **arguments**, the actual values passed in) and produce output with `return`:

```python
def add(a, b):
    return a + b

result = add(3, 4)   # result == 7
```

If a function has no `return` statement (or a bare `return` with no value), it implicitly returns `None`.

---

## 10.3 Default and Keyword Arguments

Parameters can have default values, making them optional when calling the function:

```python
def greet(name, greeting="Hello"):
    print(f"{greeting}, {name}!")

greet("Ada")                # "Hello, Ada!"
greet("Ada", "Hi")           # "Hi, Ada!"
greet(name="Ada", greeting="Hey")  # keyword arguments — order doesn't matter
```

Arguments passed by name (`name="Ada"`) are called **keyword arguments**; arguments passed by position are called **positional arguments**. Positional arguments must always come before keyword arguments in a call.

---

## 10.4 *args and **kwargs

Sometimes you don't know in advance how many arguments a function will receive.

- **`*args`** collects any number of extra positional arguments into a tuple.
- **`**kwargs`** collects any number of extra keyword arguments into a dictionary.

```python
def sum_all(*args):
    return sum(args)

sum_all(1, 2, 3, 4)   # 10

def describe(**kwargs):
    for key, value in kwargs.items():
        print(f"{key}: {value}")

describe(name="Ada", age=36)
```

The names `args` and `kwargs` are just convention — the meaningful part is the `*` and `**` prefixes.

---

## 10.5 Lambda Functions

A `lambda` is a small, anonymous, single-expression function, useful when you need a short throwaway function (commonly as an argument to another function like `sorted()` or `map()`):

```python
square = lambda x: x ** 2
square(5)   # 25

people = [("Ada", 36), ("Bo", 22)]
people.sort(key=lambda person: person[1])  # sort by age
```

Lambdas can only contain a single expression (no statements, no multiple lines) — for anything more complex, use a regular `def` function instead.

---

## 10.6 Scope and the LEGB Rule

**Scope** determines where in your code a variable name is visible. Python resolves a name using the **LEGB rule**, checking each scope in order until it finds a match:

1. **Local** — inside the current function.
2. **Enclosing** — inside any enclosing function (for nested functions).
3. **Global** — at the top level of the module/file.
4. **Built-in** — Python's built-in names (`len`, `print`, etc.).

```python
x = "global"

def outer():
    x = "enclosing"

    def inner():
        x = "local"
        print(x)   # "local"

    inner()
    print(x)       # "enclosing"

print(x)           # "global"
```

Assigning to a variable inside a function creates a new **local** variable by default, even if a global variable with the same name exists. To modify a global variable from inside a function, you must explicitly declare it with the `global` keyword (or `nonlocal` for an enclosing function's variable):

```python
counter = 0

def increment():
    global counter
    counter += 1
```

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[11]-String-Formatting.md)
