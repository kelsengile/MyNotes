[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[11]-References-vs-Pointers.md)

*Core Syntax*

# Lesson 10 - Functions And Scope

## 10.1 Declaring And Defining Functions

A function packages up reusable code. It has a return type, a name, and a parameter list:

```cpp
int add(int a, int b) {
    return a + b;
}

int main() {
    int result = add(3, 4); // result is 7
}
```

Functions can be **declared** (a prototype, telling the compiler the function exists) separately from where they're **defined** (the actual implementation) — useful when calling a function before its definition appears, or when splitting code across files:

```cpp
int add(int a, int b); // declaration

int main() {
    int result = add(3, 4); // works, because add is already declared
}

int add(int a, int b) { // definition
    return a + b;
}
```

---

## 10.2 Parameters, Return Values & Default Arguments

Functions can take multiple parameters and return at most one value directly:

```cpp
double calculateArea(double width, double height) {
    return width * height;
}
```

A function that returns nothing uses `void`:

```cpp
void printGreeting(std::string name) {
    std::cout << "Hello, " << name << "!\n";
}
```

Parameters can have **default values**, used when the caller omits that argument:

```cpp
void logMessage(std::string message, bool urgent = false) {
    std::cout << (urgent ? "[URGENT] " : "") << message << "\n";
}

logMessage("Server started");        // urgent defaults to false
logMessage("Disk full", true);       // urgent explicitly set
```

---

## 10.3 Function Overloading

C++ allows multiple functions with the **same name** but different parameter types or counts — this is **overloading**:

```cpp
int add(int a, int b) {
    return a + b;
}

double add(double a, double b) {
    return a + b;
}

std::string add(std::string a, std::string b) {
    return a + b;
}
```

The compiler picks the right version based on the argument types you pass. Overloads must differ in their parameters, not just their return type.

---

## 10.4 Scope And Lifetime

A variable's **scope** is the region of code where it's visible; its **lifetime** is how long it exists in memory. Variables declared inside a block `{}` are local to that block:

```cpp
void example() {
    int x = 10; // x's scope starts here

    {
        int y = 20; // y is only visible inside this inner block
        std::cout << x << y; // both visible here
    }

    // y is no longer visible here
    std::cout << x; // still fine
}
```

Local variables are created when their scope is entered and destroyed when it's exited. **Global variables**, declared outside any function, exist for the entire program's lifetime — but are generally best avoided, since they make code harder to reason about.

---

## 10.5 inline Functions

Marking a function `inline` is a hint asking the compiler to substitute the function's body directly at each call site, avoiding function-call overhead for small, frequently-called functions:

```cpp
inline int square(int x) {
    return x * x;
}
```

Modern compilers usually make this decision automatically based on optimization settings, regardless of the `inline` keyword, so it's mostly used today for a different reason: allowing a function to be defined in a header file included by multiple `.cpp` files without causing "multiply defined symbol" linker errors.

[Previous](./[9]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[11]-References-vs-Pointers.md)
