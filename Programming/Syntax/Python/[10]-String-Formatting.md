[Previous](./[9]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[11]-Error-Handling.md)


# Lesson 10 - String Formatting & Manipulation

---
## 10.1 f-strings


f-strings (formatted string literals, Python 3.6+) are the modern, recommended way to embed expressions in strings:

```python
name = "Ada"
age = 25
f"{name} is {age} years old"        # "Ada is 25 years old"
f"Next year: {age + 1}"             # expressions work directly
f"{3.14159:.2f}"                    # "3.14" — format spec after a colon
```

---

## 10.2 str.format() and % formatting


Two older formatting styles you'll still see in existing code:

```python
"{} is {} years old".format(name, age)     # str.format()
"%s is %d years old" % (name, age)         # % formatting (oldest style)
```

Both still work, but f-strings are generally preferred in new code for their readability and performance.

---

## 10.3 String Methods


Strings come with many built-in methods for common transformations:

```python
"  hello  ".strip()        # "hello"       — remove surrounding whitespace
"hello".upper()             # "HELLO"
"HELLO".lower()             # "hello"
"hello world".split()       # ["hello", "world"]
"-".join(["a", "b", "c"])   # "a-b-c"
"hello".replace("l", "L")   # "heLLo"
"hello".startswith("he")    # True
"hello".find("l")           # 2 — index of first match, -1 if not found
```

Because strings are immutable, every one of these returns a *new* string rather than modifying the original.

---

## 10.4 Slicing


Slicing extracts a portion of a string using `[start:stop:step]`:

```python
s = "Hello, World!"
s[0]      # "H"       — single character
s[0:5]    # "Hello"   — characters 0 up to (not including) 5
s[7:]     # "World!"  — from index 7 to the end
s[:5]     # "Hello"   — from the start to index 5
s[-6:]    # "World!"  — negative indices count from the end
s[::-1]   # "!dlroW ,olleH" — step of -1 reverses the string
```

Slicing works the same way on lists and tuples, which are covered in Lesson 12.

---

[Previous](./[9]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[11]-Error-Handling.md)
