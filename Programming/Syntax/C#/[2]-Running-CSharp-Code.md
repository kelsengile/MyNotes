[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[3]-Projects-and-Solutions.md)

*Getting Started*

# Lesson 2 - Running Code: `dotnet run`, the REPL (csharp), and Visual Studio

## 2.1 Creating and Running Your First Program

Create a new console project and run it in two commands:

```bash
dotnet new console -o HelloWorld
cd HelloWorld
dotnet run
```

`dotnet new console` scaffolds a small project with a `Program.cs` file. `dotnet run` compiles and executes it immediately, printing `Hello, World!` to the terminal.

---

## 2.2 The C# REPL

A **REPL** (Read-Eval-Print Loop) lets you type C# statements and see results instantly, without creating a project — useful for quick experiments. Install and launch one with:

```bash
dotnet tool install -g dotnet-script
dotnet script
```

Then type expressions directly:

```csharp
> int x = 5 + 3;
> x
8
```

---

## 2.3 Running Code in an Editor/IDE

In **Visual Studio Code**, open your project folder, open `Program.cs`, and press `F5` (or use the Run/Debug panel) to build and run with breakpoints and step-through debugging.

In **Visual Studio**, open the `.sln` or `.csproj` file, then press the green ▶ **Start** button (or `F5`) to run — it also supports breakpoints, variable inspection, and hot reload out of the box.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C%23.md) | [Next](./[3]-Projects-and-Solutions.md)
