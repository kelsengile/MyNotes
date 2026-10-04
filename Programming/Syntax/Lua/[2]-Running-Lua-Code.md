[Previous](./[1]-Installation-And-Setup.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[3]-LuaRocks-And-Packages.md)

*Getting Started*

# Lesson 2 - Running Code: Scripts, the REPL & Online Tools

Lua code can run in several ways: as a saved script, line by line in an interactive prompt, inside another program, or in a web page. This lesson shows each one and helps you decide when to use which.

---

## 2.1 The Lua Interpreter

The program you installed in Lesson 1, `lua`, is the **standalone interpreter**. It reads Lua source code, compiles it to bytecode in memory, and runs it on the Lua virtual machine. You rarely need to think about the compile step; it happens automatically.

The general form of the command is:

```text
lua [options] [script [args]]
```

Common options:

| Option | Meaning |
|---|---|
| `lua script.lua` | Run a file |
| `lua -e "stat"` | Execute the given statement(s) from the command line |
| `lua -i` | Run a script (if given), then enter the interactive prompt |
| `lua -l name` | `require` the module `name` before running (newer 5.4 releases also accept `-l g=mod`) |
| `lua -v` | Print the version |
| `lua -` | Read the program from standard input |
| `lua -W` | Turn on warnings |
| `lua --` | Stop handling options (everything after is an argument) |
| `lua` (no arguments) | Start the interactive prompt (REPL) when run in a terminal |

There is also `luac`, the Lua compiler, which converts source files into precompiled bytecode (`luac -o out.luac script.lua`) and can check syntax with `luac -p script.lua`. You will rarely need bytecode, but `luac -p` is a handy way to check a file for syntax errors without running it.

---

## 2.2 Running Scripts (`.lua` files)

A **script** is a text file containing Lua code, normally named with the `.lua` extension. Create `greet.lua`:

```lua
local name = "World"
print("Hello, " .. name .. "!")
```

Run it:

```text
lua greet.lua
```

Output:

```text
Hello, World!
```

**Paths.** If the file is in another folder, give the path: `lua projects/greet.lua`.

**Shebang line.** On Linux and macOS, a script can be made directly executable. Lua ignores a first line starting with `#`:

```lua
#!/usr/bin/env lua
print("I can be run directly")
```

Then run `chmod +x script.lua` and execute it with `./script.lua`.

**Exit codes.** When a script finishes normally, `lua` exits with status 0. If an uncaught error occurs, Lua prints the message and a stack traceback to standard error and exits with a non-zero status. Shell scripts can use this to detect failure. (`os.exit(code)` lets you pick your own exit code; see Lesson 30.)

**One-liners.** For quick experiments, skip the file entirely:

```text
lua -e "print(10 / 4)"
```

Output:

```text
2.5
```

---

## 2.3 The Interactive REPL

REPL stands for **Read-Eval-Print Loop**: it reads a line you type, evaluates it, prints the result, and waits for more. Start it by running `lua` with no arguments:

```text
$ lua
Lua 5.4.7  Copyright (C) 1994-2024 Lua.org, PUC-Rio
> 
```

The `>` is the prompt. Type an expression and Lua shows its value (in Lua 5.3 and later):

```text
> 1 + 2
3
> "abc" .. "def"
abcdef
> x = 10
> x * 2
20
> print("hi")
hi
```

Multi-line statements continue with a `>>` prompt until the statement is complete:

```text
> for i = 1, 3 do
>> print(i)
>> end
1
2
3
```

**Exiting.** Press `Ctrl+D` (Linux/macOS) or `Ctrl+Z` then `Enter` (Windows), or call `os.exit()`.

**Version difference:** In Lua 5.1 and 5.2 the REPL does not print expression results automatically; you had to prefix with `=`, as in `= 1 + 2`. The `=` prefix still works in 5.4.

**Local variables in the REPL.** Each line you type is its own chunk, so a `local` variable disappears after that line. Use globals (no `local`) for values you want to keep between lines in the REPL:

```text
> local a = 5
> print(a)
nil
> b = 5
> print(b)
5
```

The REPL is ideal for testing a function, exploring the standard library, or checking how an expression behaves.

---

## 2.4 Command-Line Arguments (`arg` table)

Anything after the script name is passed to the script. The interpreter stores everything in a global table named `arg`.

Create `args.lua`:

```lua
print("Script name:", arg[0])
print("Number of args:", #arg)
for i = 1, #arg do
  print(i, arg[i])
end
```

Run it:

```text
lua args.lua apple banana "red cherry"
```

Output:

```text
Script name:	args.lua
Number of args:	3
1	apple
2	banana
3	red cherry
```

How the table is laid out:

| Index | Contains |
|---|---|
| `arg[0]` | The script name |
| `arg[1]`, `arg[2]`, ... | The arguments, in order |
| `arg[-1]`, `arg[-2]`, ... | The interpreter name and its options |

The same arguments are also available as the **vararg expression** `...` in the main chunk of the script:

```lua
local first, second = ...
print(first, second)
```

All arguments arrive as **strings**. Convert numbers with `tonumber`:

```lua
local n = tonumber(arg[1]) or 10   -- default to 10 if missing or invalid
print("n squared is", n * n)
```

---

## 2.5 Running Code Inside Other Programs (LÖVE, Neovim, Roblox)

Lua is designed to be embedded, so much of the Lua you write will not be run with the `lua` command at all. Instead, a **host program** loads and runs your scripts.

**LÖVE (game framework).** You create a folder containing `main.lua` and run it by dragging the folder onto the LÖVE application or running `love path/to/folder`. LÖVE calls your `love.load`, `love.update`, and `love.draw` functions (see Lesson 36).

**Neovim.** Inside the editor you can run Lua directly:

```text
:lua print("hello from Neovim")
:luafile %
```

`:luafile %` runs the file currently open. Your configuration lives in `init.lua` (see Lesson 38).

**Roblox Studio.** You add a `Script` to a game object, press Play, and read `print` output in the Output window. Roblox uses Luau, a Lua dialect (Lesson 37).

**Other hosts.** Redis (`EVAL`), OpenResty/Nginx, Defold, World of Warcraft add-ons, and Garry's Mod are all covered in Lesson 39.

When working inside a host, the **language** is the same, but the available libraries differ. A host may remove functions (for example, the `io` library) or add its own (such as `love.graphics`).

---

## 2.6 Online Playgrounds

If you cannot install anything (for example on a school computer, a tablet, or when you just want to try a snippet), use an online playground:

- **The official demo** at <https://www.lua.org/demo.html> lets you type code and run it in the browser using real Lua.
- Many general code-running sites (such as Replit or Tio) offer Lua. Check which version they use: some run 5.1, 5.3, or LuaJIT.

Limitations to keep in mind:

- Playgrounds often cannot read files or take command-line arguments.
- Output size and run time are limited.
- You may not be running Lua 5.4. Test with `print(_VERSION)` first.

---

## 2.7 Which Should You Use?

| Situation | Best choice |
|---|---|
| Trying a single expression or checking a function's behavior | REPL |
| Writing anything you want to save or run again | A `.lua` script |
| One quick command in a shell script | `lua -e "..."` |
| Following this course | Scripts in an editor, plus the REPL for quick checks |
| Making a game or editor plugin | The host program (LÖVE, Neovim, etc.) |
| No installation possible | An online playground |

A good habit: experiment in the REPL, and when something works, paste it into a script file.

---

[Previous](./[1]-Installation-And-Setup.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[3]-LuaRocks-And-Packages.md)
