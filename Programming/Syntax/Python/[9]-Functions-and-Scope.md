[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[10]-String-Formatting.md)

# Lesson 9 - Functions & Scope

---

## 9.1 Defining and Calling Functions


```python
def greet(name):
    return f"Hello, {name}!"

greet("Ada")   # "Hello, Ada!"
```

`def` defines a function. The `return` statement sends a value back to the caller; without it, a function returns `None` by default. Functions should generally do one clear thing, taking their inputs as parameters rather than reaching for global variables.

---

## 9.2 Default, Keyword, *args and **kwargs


```python
def power(base, exponent=2):     # default value
    return base ** exponent

power(3)              # 9  (uses default exponent)
power(3, exponent=3)  # 27 (keyword argument)

def total(*args):                # collects extra positional args into a tuple
    return sum(args)

total(1, 2, 3)         # 6

def describe(**kwargs):          # collects extra keyword args into a dict
    for key, value in kwargs.items():
        print(f"{key}: {value}")

describe(name="Ada", age=25)
```

`*args` and `**kwargs` let a function accept a variable number of positional or keyword arguments — commonly used when wrapping or forwarding calls to other functions.

---

## 9.3 Lambda Functions


A `lambda` is a small, anonymous, single-expression function:

```python
square = lambda x: x ** 2
square(5)   # 25

# Most common use: as a short throwaway function argument
sorted(["banana", "kiwi", "apple"], key=lambda word: len(word))
```

Lambdas can't contain statements (like `if`/`else` blocks or loops) — only a single expression. For anything more complex, use a regular `def` function.

---

## 9.4 Scope and the LEGB Rule


Python resolves a variable name by searching four scopes in order — **L**ocal, **E**nclosing, **G**lobal, **B**uilt-in:

```python
x = "global"

def outer():
    x = "enclosing"
    def inner():
        x = "local"
        print(x)     # "local" — found immediately in Local scope
    inner()

outer()
```

If `inner()` didn't define its own `x`, Python would look in `outer()`'s scope (Enclosing), then the module level (Global), then Python's built-ins (Built-in). Assigning to a variable inside a function creates a *local* variable by default; use the `global` or `nonlocal` keyword to explicitly modify a variable from an outer scope.

---

[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[10]-String-Formatting.md)
