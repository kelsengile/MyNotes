[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[10]-The-Preprocessor.md)

*Core Syntax*

# Lesson 9 - Functions And Scope

## 9.1 Declaring and Defining Functions

A function groups reusable code under a name. Its definition specifies a return type, a name, parameters, and a body:

```c
int add(int a, int b) {
    return a + b;
}

int main(void) {
    int sum = add(3, 4);   // sum = 7
    printf("%d\n", sum);
    return 0;
}
```

A function that returns nothing uses `void` as its return type:

```c
void greet(const char *name) {
    printf("Hello, %s!\n", name);
}
```

A function taking no parameters should be declared with `void` in the parentheses, not left empty — `int foo()` in C means "unspecified arguments," not "no arguments."

---

## 9.2 Function Prototypes

C reads top to bottom. If you call a function before its full definition appears, you must first give the compiler a **prototype** — a declaration of its signature without a body:

```c
int add(int a, int b);   // prototype

int main(void) {
    printf("%d\n", add(2, 3));   // works: compiler already knows add's signature
    return 0;
}

int add(int a, int b) {   // full definition, can come after main
    return a + b;
}
```

Prototypes are usually placed in header files (`.h`) so multiple source files can share them — this is covered in depth in Lesson 22.

---

## 9.3 Parameters and Return Values

C passes arguments **by value** — the function receives a copy, so changes inside the function don't affect the caller's original variable:

```c
void increment(int x) {
    x = x + 1;   // only modifies the local copy
}

int main(void) {
    int n = 5;
    increment(n);
    printf("%d\n", n);   // still prints 5
    return 0;
}
```

To let a function modify the caller's variable, you must pass a **pointer** to it instead (pointers are introduced fully in Lesson 11):

```c
void increment(int *x) {
    *x = *x + 1;   // modifies the value the pointer points to
}

int main(void) {
    int n = 5;
    increment(&n);
    printf("%d\n", n);   // prints 6
    return 0;
}
```

---

## 9.4 Scope and Lifetime of Variables

A variable's **scope** is where in the code it can be referenced; its **lifetime** is how long it exists in memory.

```c
int global_counter = 0;   // file scope: visible to every function below it

void example(void) {
    int local = 5;         // local scope: only visible inside this function
    {
        int inner = 10;    // block scope: only visible inside these braces
    }
    // inner is not accessible here
}
```

Local variables are allocated on the **stack** and destroyed automatically when their enclosing block ends (see Lesson 14). Global variables live for the entire program's lifetime, but are best used sparingly — they make code harder to reason about, since any function can change them.

`static` local variables are a middle ground: scoped to a function, but they retain their value between calls:

```c
void counter(void) {
    static int calls = 0;   // initialized only once, ever
    calls++;
    printf("Called %d times\n", calls);
}
```

---

## 9.5 Recursion

A function that calls itself is **recursive**. Every recursive function needs a **base case** to stop the recursion, or it will run forever (and eventually crash with a stack overflow):

```c
int factorial(int n) {
    if (n <= 1) {
        return 1;          // base case
    }
    return n * factorial(n - 1);   // recursive case
}

int main(void) {
    printf("%d\n", factorial(5));   // 120
    return 0;
}
```

Each call to `factorial` gets its own copy of `n` on the stack. Recursion is elegant for problems with a naturally recursive structure (like tree traversal, covered in Lesson 21) but can be less efficient than an equivalent loop due to the overhead of repeated function calls.

---

[Previous](./[8]-Loops.md) | [Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[10]-The-Preprocessor.md)
