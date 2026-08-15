[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[12]-Error-Handling.md)

*Core Syntax*

# Lesson 11 - String Formatting & Manipulation

## 11.1 f-strings

**f-strings** (formatted string literals, introduced in Python 3.6) are the modern, recommended way to embed expressions inside a string. Prefix the string with `f` and put expressions in `{}`:

```python
name = "Ada"
age = 36

f"{name} is {age} years old"        # "Ada is 36 years old"
f"Next year: {age + 1}"              # expressions work too
f"{age:.2f}"                          # "36.00" — formatting a number
f"{name!r}"                           # "'Ada'" — uses repr()
```

---

## 11.2 The .format() Method

Before f-strings, `.format()` was the standard approach, and it's still common in older codebases:

```python
"{} is {} years old".format(name, age)
"{n} is {a} years old".format(n=name, a=age)
"{0} is {1}, {0} again".format(name, age)  # positional indices can repeat
```

---

## 11.3 % Formatting (legacy)

The oldest style, inherited from C's `printf`, using `%` as a placeholder operator. You'll still see it in some legacy code:

```python
"%s is %d years old" % (name, age)
```

For new code, prefer f-strings — they're more readable and generally faster.

---

## 11.4 String Slicing

Strings support **slicing** to extract a substring, using `[start:stop:step]` (stop is exclusive):

```python
s = "Hello, World!"

s[0]        # "H"          single character
s[0:5]      # "Hello"      characters 0 through 4
s[7:]       # "World!"     from index 7 to the end
s[:5]       # "Hello"      from the start to index 4
s[-6:]      # "World!"     negative indices count from the end
s[::-1]     # "!dlroW ,olleH"  reversed string (step of -1)
s[::2]      # "Hlo ol!"    every second character
```

---

## 11.5 Common String Methods

Since strings are immutable, every method below **returns a new string** rather than modifying the original:

```python
"Hello".upper()             # "HELLO"
"Hello".lower()              # "hello"
"  hi  ".strip()             # "hi"           removes leading/trailing whitespace
"a,b,c".split(",")           # ["a", "b", "c"]
",".join(["a", "b", "c"])    # "a,b,c"
"Hello".replace("l", "L")    # "HeLLo"
"Hello".startswith("He")     # True
"Hello".endswith("lo")       # True
"Hello".find("l")            # 2  — index of first match, -1 if not found
"hello world".title()        # "Hello World"
"Hello" in "Hello, World!"   # True — substring membership check
```

[Previous](./[10]-Functions-and-Scope.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[12]-Error-Handling.md)
