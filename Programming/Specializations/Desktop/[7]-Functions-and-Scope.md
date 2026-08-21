[Previous](./[6]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[8]-Object-Oriented-Basics.md)

*Core Syntax*

# Lesson 7 - Functions & Scope

## 7.1 Defining Functions

A function packages reusable logic behind a name, parameters, and (optionally) a return value:

```csharp
int CalculateTotal(List<int> prices)
{
    int total = 0;
    foreach (var price in prices) total += price;
    return total;
}
```

Keeping functions small and focused on one task makes them easier to test in isolation and easier to reuse across a UI's event handlers.

---

## 7.2 Parameters: By Value vs By Reference

Primitive types are usually passed **by value** (the function gets a copy), while objects are usually passed **by reference** (the function gets a pointer/handle to the same underlying data). This matters in desktop code: mutating a passed-in list or UI control inside a function affects the original, which can cause subtle bugs if not intentional.

---

## 7.3 Scope

A variable's **scope** is the region of code where it's visible. Block scope (`{ }`) is standard in modern C-family languages — a variable declared inside an `if` block or loop isn't visible outside it. Desktop UI frameworks add a layer: fields declared at the class level (e.g. a window's member variables) are visible to every event handler in that class, which is how a button's click handler can read a text field's current value.

---

## 7.4 Closures and Callbacks

A closure captures variables from its enclosing scope, which is essential for event-driven UI code where a callback runs later, after the function that registered it has returned:

```javascript
function setupCounter(button) {
  let count = 0;
  button.addEventListener('click', () => {
    count++; // captured from the enclosing scope
    updateLabel(count);
  });
}
```

Closures are the backbone of event handling, covered in depth in Lesson 15.

[Previous](./[6]-Control-Flow.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[8]-Object-Oriented-Basics.md)
