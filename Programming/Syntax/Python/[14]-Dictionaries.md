[Previous](./[13]-Lists-Tuples-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[15]-Comprehensions.md)

*Data Structures*

# Lesson 14 - Dictionaries

## 14.1 What is a Dictionary?

A **dictionary** (`dict`) stores data as key-value pairs, giving fast lookups by key instead of position. Keys must be unique and hashable (strings, numbers, or tuples); values can be anything.

```python
person = {
    "name": "Ada",
    "age": 36,
    "is_admin": True,
}
```

Since Python 3.7, dictionaries preserve **insertion order** as a language guarantee.

---

## 14.2 Creating and Accessing Dictionaries

```python
person["name"]           # "Ada"
person.get("name")        # "Ada" — safer, returns None instead of raising an error
person.get("email", "n/a")  # "n/a" — custom default if key is missing

person["email"]           # KeyError: 'email' — raises an error if key doesn't exist
```

Use `.get()` whenever a key might not exist and you don't want your program to crash.

---

## 14.3 Adding, Updating, Removing Items

```python
person["email"] = "ada@example.com"   # add a new key
person["age"] = 37                     # update an existing key

del person["is_admin"]                 # remove a key
person.pop("age")                       # remove & return the value
person.setdefault("role", "user")       # set only if key doesn't already exist
```

---

## 14.4 Iterating Over Dictionaries

```python
for key in person:                # iterates over keys by default
    print(key)

for key, value in person.items(): # iterate over key-value pairs
    print(key, value)

for value in person.values():     # iterate over values only
    print(value)
```

---

## 14.5 Dictionary Methods

```python
person.keys()      # a view of all keys
person.values()     # a view of all values
person.items()      # a view of all (key, value) pairs
person.update({"age": 40, "city": "NYC"})  # merge another dict in, overwriting matches
"name" in person     # True — checks keys, not values
len(person)          # number of key-value pairs
person.clear()       # removes all items
```

---

## 14.6 Nested Dictionaries

Dictionaries can contain other dictionaries (or lists) as values, which is how most real-world structured data (like JSON from an API) is represented in Python:

```python
users = {
    "ada": {"age": 36, "roles": ["admin", "editor"]},
    "bo": {"age": 22, "roles": ["viewer"]},
}

users["ada"]["age"]           # 36
users["ada"]["roles"][0]       # "admin"
users["ada"]["roles"].append("owner")
```

[Previous](./[13]-Lists-Tuples-and-Sets.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[15]-Comprehensions.md)
