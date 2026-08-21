[Previous](./[3]-Anatomy-of-a-Desktop-App-Project.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[5]-Variables-and-Data-Types.md)

*Getting Started*

# Lesson 4 - Command-Line Interfaces & Console Apps

## 4.1 Why CLIs Matter in Desktop Development

Not every desktop app has a window. Command-line tools are still "desktop development" because they run natively on the user's machine, read arguments, print to a terminal, and exit with a status code. Many GUI apps also ship a companion CLI (e.g. `code` for VS Code) for scripting and automation.

---

## 4.2 Reading Arguments and Producing Output

A console app receives arguments via `argv`/`args`, reads input from `stdin`, and writes results to `stdout` (and errors to `stderr`). Example in C#:

```csharp
static void Main(string[] args)
{
    if (args.Length == 0)
    {
        Console.Error.WriteLine("Usage: mytool <file>");
        Environment.Exit(1);
    }
    Console.WriteLine($"Processing {args[0]}...");
}
```

Exit codes matter: `0` conventionally means success, any nonzero value signals an error, which lets shell scripts and CI pipelines chain commands reliably.

---

## 4.3 Building Friendlier CLIs

Raw argument parsing gets unwieldy fast, so most ecosystems have an argument-parsing library — `System.CommandLine` for .NET, `argparse`/`click` for Python, `clap` for Rust, `yargs`/`commander` for Node. These provide automatic `--help` text, flag validation, and subcommands (e.g. `git commit`, `git push`) for free.

---

## 4.4 CLIs vs GUI Apps

CLIs are faster to build, easy to automate, and low-overhead, but they demand comfort with a terminal. GUI apps are more discoverable and accessible to non-technical users but require the windowing, layout, and event-handling machinery covered starting in Lesson 10. Many real projects offer both, sharing a common core library between them.

[Previous](./[3]-Anatomy-of-a-Desktop-App-Project.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[5]-Variables-and-Data-Types.md)
