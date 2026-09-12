[Previous](./[7]-The-Integrated-Terminal.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[9]-Advanced-Debugging-Techniques.md)

*Debugging*

# Lesson 8 - Introduction To Debugging

## 8.1 What Is a Debugger

A debugger is a tool that runs your program under controlled conditions so you can pause it, inspect its current state, and step through it line by line, rather than just reading error output after the fact. Instead of guessing what a piece of code is doing by adding print statements everywhere, a debugger lets you watch it happen in real time.

---

## 8.2 Breakpoints

A breakpoint marks a specific line where execution should pause once the program reaches it, usually set by clicking in the margin next to that line. When the program hits a breakpoint, it freezes exactly there, letting you examine every variable's current value before deciding what to do next.

---

## 8.3 Stepping Through Code

Once paused at a breakpoint, a debugger gives you fine-grained control over what runs next:

- **Step Over** — run the current line and move to the next one, without entering any function it calls.
- **Step Into** — jump inside a function call to see what happens line by line within it.
- **Step Out** — finish running the current function and pause again right after it returns.
- **Continue** — resume normal execution until the next breakpoint or the program ends.

---

## 8.4 Watch Variables And The Call Stack

While paused, a **Variables panel** shows the current value of everything in scope, and a **Watch panel** lets you pin specific expressions to track as you step through the code. The **Call Stack panel** shows the chain of function calls that led to the current line, which is essential for understanding how execution actually arrived where it is — especially in code with many nested function calls.

[Previous](./[7]-The-Integrated-Terminal.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[9]-Advanced-Debugging-Techniques.md)
