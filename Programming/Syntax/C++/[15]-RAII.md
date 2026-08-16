[Previous](./[14]-Dynamic-Memory.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[16]-Smart-Pointers.md)

*Memory Management*

# Lesson 15 - RAII

## 15.1 The Problem RAII Solves

Manual resource management — remembering to call `delete`, `close()`, or `unlock()` — is error-prone, especially once exceptions are involved:

```cpp
void process() {
    int* data = new int[100];
    doWork(data);       // if this throws, delete[] is never reached
    delete[] data;      // leaked if an exception was thrown above
}
```

Any early return or thrown exception between acquiring a resource and releasing it can skip the cleanup entirely, causing a leak.

---

## 15.2 How RAII Works

**RAII** stands for **Resource Acquisition Is Initialization**. The idea: tie a resource's lifetime to an object's lifetime. Acquire the resource in the object's **constructor**, release it in the object's **destructor**. Since C++ guarantees destructors run automatically when an object goes out of scope — including during stack unwinding from an exception — the cleanup can never be skipped.

```cpp
class FileHandle {
public:
    FileHandle(const std::string& path) {
        file = std::fopen(path.c_str(), "r");
    }
    ~FileHandle() {
        if (file) std::fclose(file); // always runs, even if an exception occurs
    }
private:
    FILE* file;
};

void process() {
    FileHandle handle("data.txt"); // resource acquired
    doWork(handle);
    // no explicit cleanup needed — the destructor handles it automatically
}
```

---

## 15.3 RAII In Practice

You rarely need to write RAII wrappers yourself — the standard library already provides them for the most common resources:

- **`std::vector`, `std::string`** — manage their own dynamically allocated buffers
- **`std::unique_ptr`, `std::shared_ptr`** — manage heap-allocated objects (covered next lesson)
- **`std::lock_guard`** — releases a mutex automatically, even if an exception occurs mid-critical-section
- **`std::fstream`** — closes its file automatically when it goes out of scope

```cpp
#include <mutex>

std::mutex m;

void safeIncrement(int& counter) {
    std::lock_guard<std::mutex> lock(m); // acquires the mutex
    counter++;
} // lock is released automatically here, even on exception
```

---

## 15.4 RAII Beyond Memory

RAII isn't limited to memory — it applies to any resource with an "acquire" and "release" step: file handles, network sockets, mutexes, database connections, even a simple debug timer that logs elapsed time when it goes out of scope. The consistent pattern — acquire in the constructor, release in the destructor — is one of the defining idioms of well-written C++, and it's why C++ code rarely uses explicit `try`/`finally`-style constructs found in other languages: destructors already play that role.

[Previous](./[14]-Dynamic-Memory.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[16]-Smart-Pointers.md)
