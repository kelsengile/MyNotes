[Previous](./[18]-Arrays-and-C-Strings.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[20]-STL-Sequence-Containers.md)

*Data Structures*

# Lesson 19 - std::string And String Manipulation

## 19.1 Creating And Initializing Strings

`std::string`, from `<string>`, manages its own memory, grows dynamically, and knows its own length — solving the main pitfalls of C-style strings from the previous lesson:

```cpp
#include <string>

std::string empty;                 // ""
std::string greeting = "Hello";    // from a literal
std::string copy = greeting;       // copy of another string
std::string repeated(3, 'x');      // "xxx"
```

---

## 19.2 Concatenation And Comparison

```cpp
std::string first = "Hello";
std::string second = "World";

std::string combined = first + ", " + second + "!"; // "Hello, World!"
first += " there"; // appends in place: "Hello there"

bool equal = (first == "Hello there"); // true
bool alphabetical = ("apple" < "banana"); // true, lexicographic comparison
```

---

## 19.3 Accessing And Modifying Characters

```cpp
std::string word = "hello";

char first = word[0];       // 'h', no bounds checking
char safe = word.at(0);     // 'h', throws std::out_of_range if index is invalid

word[0] = 'H';              // "Hello"
std::cout << word.length(); // 5 (equivalent to word.size())

word.push_back('!');        // "Hello!"
word.pop_back();            // back to "Hello"
```

---

## 19.4 Searching And Substrings

```cpp
std::string sentence = "The quick brown fox";

size_t pos = sentence.find("quick"); // 4 (index where "quick" starts)
if (pos != std::string::npos) {      // npos means "not found"
    std::cout << "Found at " << pos << "\n";
}

std::string sub = sentence.substr(4, 5); // "quick" — starts at 4, length 5

std::string replaced = sentence;
replaced.replace(4, 5, "slow"); // "The slow brown fox"
```

---

## 19.5 Converting Between Strings And Numbers

```cpp
#include <string>

std::string numText = "42";
int num = std::stoi(numText);     // string -> int
double d = std::stod("3.14");     // string -> double

int value = 100;
std::string text = std::to_string(value); // int -> string: "100"
```

These conversion functions throw `std::invalid_argument` if the string doesn't contain a valid number, and `std::out_of_range` if the number is too large to fit — worth wrapping in a `try`/`catch` (see **Error Handling**) when parsing untrusted input.

[Previous](./[18]-Arrays-and-C-Strings.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[20]-STL-Sequence-Containers.md)
