[Previous](./[2]-The-JetBrains-Interface.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[4]-Running-And-Debugging-Projects.md)

*Core IDE Features*

# Lesson 3 - Code Intelligence And Navigation

## 3.1 Code Completion And Suggestions

JetBrains IDEs offer several layers of completion, each triggered slightly differently:

- **Basic Completion** (`Ctrl+Space`) — suggests variables, methods, and keywords visible in the current scope.
- **Smart Completion** (`Ctrl+Shift+Space`) — filters suggestions to only what's type-compatible at the cursor. For example, inside a method expecting a `boolean`, Smart Completion hides everything except boolean-returning expressions.
- **Postfix Completion** — type a value followed by `.` and a keyword to wrap it, e.g. typing `myList.for` and selecting the suggestion expands into a `for` loop over `myList`.

```java
// Typing this:
myList.for<TAB>

// Expands into:
for (String item : myList) {

}
```

Completion suggestions are ranked using the project's own code — if you frequently call `repository.findById(...)`, that call rises higher in future suggestions than a same-named but unused method elsewhere.

---

## 3.2 Go To Declaration/Usage/Implementation

These three navigation actions answer three different questions about a symbol (variable, method, or class):

| Action | Shortcut (Win/Linux) | Question It Answers |
|---|---|---|
| Go to Declaration | `Ctrl+B` | "Where is this defined?" |
| Go to Implementation | `Ctrl+Alt+B` | "Which class(es) actually implement this interface/abstract method?" |
| Find Usages | `Alt+F7` | "Everywhere this is used across the project" |

```
interface Shape {
    double area();       ← Go to Declaration lands here
}

class Circle implements Shape {
    double area() { ... } ← Go to Implementation lands here
}

// Anywhere shape.area() is called:
shape.area();             ← Find Usages lists every one of these
```

Find Usages is especially valuable before renaming or deleting something — it shows the full blast radius of a change before you make it.

---

## 3.3 Refactoring Tools (Rename, Extract Method)

Refactoring is JetBrains' signature strength: changes are applied safely across every file that references the changed code, not just the current file.

- **Rename** (`Shift+F6`) — renames a symbol and updates every reference project-wide, including in comments and strings if you opt in.
- **Extract Method** (`Ctrl+Alt+M`) — select a block of code and turn it into its own named method, with parameters inferred automatically from the variables used.
- **Change Signature** (`Ctrl+F6`) — add, remove, or reorder a method's parameters, and the IDE updates every call site to match.
- **Inline** (`Ctrl+Alt+N`) — the reverse of Extract: replaces a variable or method call with its actual value/body.

```java
// Before Extract Method:
double total = price * quantity * (1 - discount);
System.out.println(total);

// Select "price * quantity * (1 - discount)", then Extract Method:
double total = calculateTotal(price, quantity, discount);
System.out.println(total);

private double calculateTotal(double price, double quantity, double discount) {
    return price * quantity * (1 - discount);
}
```

Because refactors are applied through the IDE's understanding of the code (not text search-and-replace), they are far safer than manually editing every occurrence.

---

## 3.4 Code Inspections And Quick-Fixes

Inspections run continuously in the background, flagging potential bugs, style issues, and unused code with colored underlines:

- **Red underline** — a compile error or definite bug (e.g. calling a method that doesn't exist).
- **Yellow/gray underline or highlight** — a warning, such as an unused variable or a deprecated API call.
- **Light bulb icon** (`Alt+Enter`) — appears next to flagged code and offers a Quick-Fix, such as adding a missing import, implementing unimplemented interface methods, or simplifying an expression.

```
if (x == true) {   ⚠ "Condition can be simplified" → Alt+Enter → if (x) {
```

Inspections can be tuned per-project in **Settings → Editor → Inspections**, so a team can enforce (or silence) specific rules — for example, disabling a naming-convention warning that conflicts with a legacy codebase's style.

[Previous](./[2]-The-JetBrains-Interface.md) | [Table of Contents](./[0]-Introduction-to-JetBrainsIDE.md) | [Next](./[4]-Running-And-Debugging-Projects.md)
