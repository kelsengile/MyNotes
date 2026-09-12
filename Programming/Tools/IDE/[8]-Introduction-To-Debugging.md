[Previous](./[7]-The-Integrated-Terminal.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[9]-Advanced-Debugging-Techniques.md)

*Debugging*

# Lesson 8 - Introduction To Debugging

## 8.1 What Is a Debugger

A debugger is a tool that runs your program under controlled conditions so you can pause it, inspect its current state, and step through it line by line, rather than just reading error output after the fact. Instead of guessing what a piece of code is doing by adding print statements everywhere, a debugger lets you watch it happen in real time.

| Approach | How you inspect state | Downside |
|---|---|---|
| Print statements | Read logged values in the console | Requires editing code, easy to forget to remove |
| Debugger | Pause and inspect live variables | Needs a moment of setup, but nothing to clean up |

---

## 8.2 Breakpoints

A breakpoint marks a specific line where execution should pause once the program reaches it, usually set by clicking in the margin next to that line. When the program hits a breakpoint, it freezes exactly there, letting you examine every variable's current value before deciding what to do next.

**Example:** with a breakpoint set on this line —

```python
total = price * quantity  # <- breakpoint here
```

— execution pauses right before this line runs, and the Variables panel shows the current values of `price` and `quantity`, letting you confirm they're what you expect *before* `total` gets calculated.

---

## 8.3 Stepping Through Code

Once paused at a breakpoint, a debugger gives you fine-grained control over what runs next:

- **Step Over** — run the current line and move to the next one, without entering any function it calls.
- **Step Into** — jump inside a function call to see what happens line by line within it.
- **Step Out** — finish running the current function and pause again right after it returns.
- **Continue** — resume normal execution until the next breakpoint or the program ends.

**Example:** paused inside `main()` on a line that calls `processOrder(order)` —

- **Step Over** treats `processOrder` as a black box and pauses on the line right after it in `main()`.
- **Step Into** jumps inside `processOrder` itself, pausing on its very first line.
- **Step Out** (once inside `processOrder`) runs the rest of that function and pauses back in `main()`, right after the call.

---

## 8.4 Watch Variables And The Call Stack

While paused, a **Variables panel** shows the current value of everything in scope, and a **Watch panel** lets you pin specific expressions to track as you step through the code. The **Call Stack panel** shows the chain of function calls that led to the current line, which is essential for understanding how execution actually arrived where it is — especially in code with many nested function calls.

**Example call stack** while paused inside a deeply nested error:

```
validateInput()      <- currently paused here
  processOrder()
    checkoutCart()
      onCheckoutButtonClick()
```

Reading from the top down shows exactly which click, function, and function-within-a-function led to the current line — without that trail, tracking down how execution got there would mean re-reading the whole program.

---

[Previous](./[7]-The-Integrated-Terminal.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[9]-Advanced-Debugging-Techniques.md)
