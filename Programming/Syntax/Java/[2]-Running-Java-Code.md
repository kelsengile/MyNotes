[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[3]-How-Java-Works.md)

*Getting Started*

# Lesson 2 - Running Code: javac, java, jshell REPL & IDEs

Now that the JDK is installed, it's time to actually compile and run some Java. This lesson covers the four most common ways to execute Java code.

## 2.1 Compiling with javac

Java is a compiled language: before it can run, your `.java` source file must be turned into `.class` bytecode by the compiler, `javac`.

Create a file named `Hello.java`:

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

Compile it:

```
javac Hello.java
```

This produces `Hello.class` in the same folder — the bytecode file the JVM can execute.

---

## 2.2 Running with java

Once compiled, run the program with the `java` launcher, referencing the class name (no `.class` extension):

```
java Hello
```

This starts the JVM, loads `Hello.class`, and calls its `main` method — the entry point of every standalone Java application.

---

## 2.3 Single-File Source-Code Programs

For quick scripts and learning exercises, modern Java lets you skip the separate compile step entirely:

```
java Hello.java
```

This compiles and runs the file in memory in one step. It's convenient for small programs, but production code is still built and compiled ahead of time.

---

## 2.4 The jshell REPL

`jshell` is Java's interactive **REPL** (Read-Eval-Print Loop), useful for experimenting with snippets of code without creating a full file.

```
jshell
jshell> int x = 5;
jshell> System.out.println(x * 2);
10
jshell> /exit
```

It's a great way to quickly test how a method or expression behaves while you're learning.

---

## 2.5 Using an IDE (IntelliJ, Eclipse, VS Code)

While the command line works fine for small examples, most Java developers use an **IDE (Integrated Development Environment)** for real projects, since it provides autocomplete, error checking, debugging, and project management:

- **IntelliJ IDEA** — the most popular Java IDE, with a free Community Edition.
- **Eclipse** — a long-standing, free, open-source IDE.
- **Visual Studio Code** — a lightweight editor that becomes a capable Java IDE with the "Extension Pack for Java" installed.

Any of these will let you create a project, write code with helpful autocomplete, and run it with a single click — you don't need to memorize `javac`/`java` commands for day-to-day work, but it's important to understand what's happening underneath.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[3]-How-Java-Works.md)