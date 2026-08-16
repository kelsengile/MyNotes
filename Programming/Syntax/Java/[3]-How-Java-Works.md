[Previous](./[2]-Running-Java-Code.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[4]-Build-Tools-Maven-and-Gradle.md)

*Getting Started*

# Lesson 3 - How Java Works: Bytecode, the JVM & Classloading

You've compiled and run a program — now let's look under the hood at what actually happens when Java code executes.

## 3.1 From Source Code to Bytecode

When you run `javac`, it doesn't turn your `.java` file into machine code the way a C compiler would. Instead, it produces **bytecode** — a platform-independent set of instructions stored in a `.class` file. Bytecode isn't tied to Windows, macOS, or Linux; it's tied to the JVM specification.

---

## 3.2 The Java Virtual Machine

The **JVM** is a program that reads bytecode and executes it on the underlying operating system and hardware. It handles memory allocation, garbage collection, and translating bytecode instructions into real operations the CPU can perform.

Because the JVM sits between your code and the machine, your compiled `.class` files behave the same way regardless of what's running underneath them.

---

## 3.3 Write Once, Run Anywhere

This is the famous Java promise: compile your code once, and run the resulting bytecode on **any** device with a compatible JVM, without recompiling. A `.class` file built on Linux runs identically on Windows or macOS, as long as each has a JVM installed.

This is different from languages like C, where you must recompile source code separately for each target platform.

---

## 3.4 Classloading

Before a class's code can run, the JVM must **load** it. This happens through the **classloading** process:

1. **Loading** — the JVM locates a `.class` file (on disk, in a JAR, etc.) and reads its bytecode into memory.
2. **Linking** — the bytecode is verified for correctness, memory is prepared for static fields, and symbolic references are resolved.
3. **Initialization** — static initializers and static field assignments run.

Classes are loaded **lazily** — only when they're first referenced by running code — which is why a large application doesn't need to load every class at startup.

---

## 3.5 JIT Compilation

Interpreting bytecode instruction-by-instruction is flexible but slower than running native machine code. To close that gap, the JVM uses a **Just-In-Time (JIT) compiler**, which watches your program as it runs and compiles "hot" (frequently executed) bytecode directly into native machine code on the fly.

This is why Java programs often start a little slower but speed up as they run — the JIT compiler is optimizing the code in real time. We'll go much deeper on this in the [JVM Tuning & JIT Compilation](./[108]-JVM-Tuning-and-JIT.md) lesson later in the course.

---

[Previous](./[2]-Running-Java-Code.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[4]-Build-Tools-Maven-and-Gradle.md)