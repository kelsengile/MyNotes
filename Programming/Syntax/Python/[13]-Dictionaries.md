[Previous](./[12]-Lists-Tuples-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[14]-Comprehensions.md)

# Lesson 13 - Dictionaries

---

## 13.1 Creating and Accessing Dictionaries


A `dict` stores key-value pairs, with fast lookup by key. Keys must be unique and hashable (strings, numbers, tuples); values can be anything.

```python
person = {"name": "Ada", "age": 25}
person["name"]           # "Ada"
person.get("email")      # None — no error, unlike person["email"]
person.get("email", "N/A")  # "N/A" — custom default
```

Accessing a missing key with `[]` raises a `KeyError`; `.get()` is the safer way to look up keys that might not exist.

---

## 13.2 Modifying Dictionaries


```python
person["email"] = "ada@example.com"   # add or overwrite a key
del person["age"]                        # remove a key
person.pop("email")                       # remove and return the value
person.update({"age": 26, "city": "NYC"}) # merge in multiple pairs
```

Dictionaries are mutable, so they follow the same reference-sharing behavior as lists (see Lesson 12.1) — copy with `.copy()` when you need an independent version.

---

## 13.3 Dictionary Methods


```python
person.keys()      # dict_keys(['name', 'age', 'city'])
person.values()     # dict_values(['Ada', 26, 'NYC'])
person.items()       # dict_items([('name', 'Ada'), ('age', 26), ('city', 'NYC')])
"name" in person      # True — checks keys by default
len(person)            # number of key-value pairs
```

`.keys()`, `.values()`, and `.items()` return view objects that reflect live changes to the dictionary, rather than a static snapshot.

---

## 13.4 Iterating Over Dictionaries


```python
for key in person:
    print(key, person[key])

for key, value in person.items():
    print(f"{key}: {value}")
```

Since Python 3.7, dictionaries preserve **insertion order** — iterating produces keys in the order they were added, which is a language guarantee, not just an implementation detail.

---

[Previous](./[12]-Lists-Tuples-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[14]-Comprehensions.md)
