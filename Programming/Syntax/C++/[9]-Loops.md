[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[10]-Functions-and-Scope.md)

*Core Syntax*

# Lesson 9 - Loops

## 9.1 for Loops

A classic `for` loop is ideal when you know how many times you want to repeat something, or need an index:

```cpp
for (int i = 0; i < 5; i++) {
    std::cout << i << "\n"; // prints 0 through 4
}
```

It has three parts, separated by semicolons: an **initialization** (`int i = 0`), a **condition** checked before each iteration (`i < 5`), and an **update** run after each iteration (`i++`).

---

## 9.2 Range-Based for Loops

Since C++11, a **range-based for loop** iterates directly over the elements of a container, without manual indexing:

```cpp
std::vector<int> scores = {90, 85, 78, 92};

for (int score : scores) {
    std::cout << score << "\n";
}

// use a reference to avoid copying, and const if you won't modify it
for (const int& score : scores) {
    std::cout << score << "\n";
}
```

This is generally preferred over an indexed loop when you don't need the index itself, since it's shorter and can't go out of bounds.

---

## 9.3 while And do-while Loops

A `while` loop repeats as long as its condition holds, checked **before** each iteration:

```cpp
int count = 0;
while (count < 3) {
    std::cout << count << "\n";
    count++;
}
```

A `do-while` loop checks its condition **after** each iteration, guaranteeing the body runs at least once:

```cpp
int input;
do {
    std::cout << "Enter a positive number: ";
    std::cin >> input;
} while (input <= 0);
```

---

## 9.4 break, continue, And Infinite Loops

`break` exits a loop immediately; `continue` skips to the next iteration:

```cpp
for (int i = 0; i < 10; i++) {
    if (i == 5) break;       // stop entirely once i reaches 5
    if (i % 2 == 0) continue; // skip even numbers
    std::cout << i << "\n";   // prints 1, 3
}
```

An **infinite loop** repeats forever unless something inside it breaks out:

```cpp
while (true) {
    // ... do work ...
    if (shouldStop) break;
}
```

Infinite loops are common in programs that run continuously, like servers or games, but make sure there's always a way to exit — otherwise the program will hang.

[Previous](./[8]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[10]-Functions-and-Scope.md)
