[Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[2]-Running-Java-Code.md)

*Getting Started*

# Lesson 1 - Installing Java & First-Time Setup (JDK, JRE, JVM)

Before writing any Java code, you need the right tools installed on your machine. This lesson walks through the pieces that make Java run, and how to get set up on Windows, macOS, or Linux.

## 1.1 JDK vs JRE vs JVM

These three acronyms are the foundation of the Java platform, and it's easy to confuse them:

- **JVM (Java Virtual Machine)** — the program that actually executes Java bytecode. It's what makes "write once, run anywhere" possible, since every platform has its own JVM implementation that understands the same bytecode.
- **JRE (Java Runtime Environment)** — the JVM plus the standard class libraries needed to *run* Java programs. It does not include tools for writing or compiling code.
- **JDK (Java Development Kit)** — the JRE plus development tools like the compiler (`javac`), debugger, and other utilities. This is what you need to *write and build* Java programs.

As a learner, you should install the **JDK** — it includes everything the JRE has, plus what you need to develop.

---

## 1.2 Choosing a Java Version (LTS Releases)

Java releases a new version every six months, but not every version is meant for long-term use. Oracle and the OpenJDK community designate certain versions as **LTS (Long-Term Support)** releases — these get years of updates and are the safest choice for learning and production use (examples include Java 17 and Java 21).

For this course, install the **latest available LTS version** unless you have a specific reason to use another one.

---

## 1.3 Installing the JDK

You can install a JDK distribution on any major operating system:

- **Windows/macOS/Linux** — download an OpenJDK build from a trusted distribution such as Eclipse Temurin, Amazon Corretto, or Oracle's own JDK, and run the installer for your OS.
- **macOS** — you can alternatively install via Homebrew: `brew install openjdk`.
- **Linux** — most distributions offer a JDK package through their package manager, e.g. `sudo apt install openjdk-21-jdk` on Debian/Ubuntu.

Pick one distribution and stick with it — mixing multiple JDK installs can cause version conflicts later.

---

## 1.4 Verifying Your Installation

Once installed, open a terminal or command prompt and run:

```
java -version
javac -version
```

Both commands should print a version number. If you see a "command not found" error, the JDK's `bin` folder likely isn't on your system `PATH` — see the next section.

---

## 1.5 Setting JAVA_HOME and PATH

Many build tools and IDEs look for an environment variable called `JAVA_HOME` that points to your JDK installation folder.

- **Windows** — set `JAVA_HOME` to the JDK install path (e.g. `C:\Program Files\Java\jdk-21`) via System Properties → Environment Variables, and add `%JAVA_HOME%\bin` to `PATH`.
- **macOS/Linux** — add these lines to your shell profile (`.zshrc`, `.bashrc`, etc.):

```
export JAVA_HOME=/path/to/your/jdk
export PATH=$JAVA_HOME/bin:$PATH
```

Restart your terminal and re-run `java -version` to confirm everything is wired up correctly. With the JDK installed and verified, you're ready to actually run some code.

---

[Table of Contents](./[0]-Introduction-to-Java.md) | [Next](./[2]-Running-Java-Code.md)