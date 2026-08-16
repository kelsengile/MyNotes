[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - C++ Standards

## 4.1 What Is A C++ Standard

C++ is defined by an official ISO standard document, revised periodically. Each revision — informally named after the year it was published — adds new language features and library components while keeping the language backward-compatible. Compilers implement these standards to varying degrees, so it's important to know which one your code targets.

---

## 4.2 Overview Of C++11 Through C++23

- **C++11** — a major overhaul: `auto`, range-based `for`, lambdas, smart pointers, move semantics, `nullptr`.
- **C++14** — smaller refinements: generic lambdas, relaxed `constexpr` rules.
- **C++17** — `std::optional`, `std::variant`, structured bindings, `if constexpr`, filesystem library.
- **C++20** — concepts, ranges, coroutines, modules, `std::span`, three-way comparison (`<=>`).
- **C++23** — further library additions like `std::expected`, `std::print`, and more ranges support.

This course uses modern C++ (C++17 and later) as its baseline, since it reflects how C++ is commonly written today.

---

## 4.3 Specifying The Standard When Compiling

You choose the standard with a compiler flag:

```bash
g++ -std=c++17 main.cpp -o main
clang++ -std=c++20 main.cpp -o main
```

With MSVC:

```bash
cl /std:c++20 main.cpp
```

If you don't specify a standard, the compiler falls back to its own default, which varies by version — always set it explicitly for predictable behavior.

---

## 4.4 Checking Feature Support

Not every compiler implements every feature of the newest standards immediately. Two common ways to check:

- Consult a compatibility table, such as the one maintained at [cppreference.com/w/cpp/compiler_support](https://en.cppreference.com/w/cpp/compiler_support).
- Use **feature-test macros** in code to check for support conditionally:

```cpp
#if __cpp_lib_optional
    // std::optional is available
#endif
```

When in doubt, pick a standard that's a couple of years old (e.g. C++17 or C++20) for the widest compatibility across compilers and platforms.

[Previous](./[3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[5]-Variables-and-Data-Types.md)
