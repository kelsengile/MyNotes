[Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[2]-Running-CSharp-Code.md)

*Getting Started*

# Lesson 1 - Installing .NET SDK & First-Time Setup

## 1.1 What is the .NET SDK?

The **.NET SDK** (Software Development Kit) is the set of tools you need to build and run C# programs. It includes the C# compiler, the `dotnet` command-line interface (CLI), and the runtime needed to execute your code. You only need the SDK installed once per machine — it works the same way on Windows, macOS, and Linux.

---

## 1.2 Installing the .NET SDK

1. Go to [dotnet.microsoft.com/download](https://dotnet.microsoft.com/download) and download the latest **LTS (Long-Term Support)** version of the SDK for your operating system.
2. Run the installer and follow the prompts.
3. Restart your terminal so it picks up the new `dotnet` command.

On Linux, you can often install it through your package manager instead, for example:

```bash
sudo apt update && sudo apt install dotnet-sdk-8.0
```

---

## 1.3 Verifying Your Installation

Open a terminal and run:

```bash
dotnet --version
```

If the SDK installed correctly, this prints a version number like `8.0.100`. You can also run `dotnet --info` to see a full breakdown of the SDK, runtime, and platform details installed on your machine.

---

## 1.4 Choosing an Editor

C# doesn't require a specific editor, but two options cover most beginners:

- **Visual Studio Code** — free, lightweight, cross-platform. Install the *C# Dev Kit* extension for IntelliSense, debugging, and project support.
- **Visual Studio** (Windows/macOS) — a full-featured IDE with a built-in designer, more powerful debugging tools, and project templates, best suited for larger applications.

Either works fine for this course — pick whichever feels more comfortable.

[Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[2]-Running-CSharp-Code.md)
