[Previous](./[0]-Introduction-to-Lua.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[2]-Running-Lua-Code.md)

*Getting Started*

# Lesson 1 - Installing Lua & First-Time Setup

Before you can write Lua, you need the Lua interpreter on your computer and a place to type code. This lesson walks through installing Lua on every major system, confirming that it works, understanding the different "flavors" of Lua, and picking an editor.

---

## 1.1 What You Need Before You Start

You need very little:

- A computer running Windows, macOS, or Linux (Lua also runs on many other systems, including phones and microcontrollers).
- A **terminal** (Command Prompt/PowerShell on Windows, Terminal on macOS and Linux). You will type a few commands to install and run Lua.
- A **text editor**. Any plain-text editor works, but a code editor makes life easier (see 1.8).
- An internet connection for downloading Lua.

Lua itself is tiny: the whole interpreter is only a few hundred kilobytes, so installation is fast.

> **Tip:** Lua needs only two things from you: a text file ending in `.lua` and an interpreter program (usually named `lua`) to run it. That is the whole workflow.

---

## 1.2 Installing Lua on Windows

Windows does not ship with Lua. You have several options:

**Option A: A package manager (recommended).** If you use [Scoop](https://scoop.sh) or [Chocolatey](https://chocolatey.org), open a terminal and run one of:

```text
scoop install lua
```

```text
choco install lua
```

**Option B: Pre-built binaries.** The Lua project does not publish official Windows binaries, but the *LuaBinaries* project does. Download the archive for your architecture, unzip it to a folder such as `C:\Lua`, and add that folder to your `PATH` environment variable so the `lua` command works from any terminal.

**Option C: Windows Subsystem for Linux (WSL).** If you already use WSL, open your Linux distribution and follow the Linux instructions in 1.4. This gives you the same experience as most Lua developers on Linux.

**Option D: Build from source.** See 1.5.

> **Note:** On some Windows installs the executable has a version in its name (for example `lua54.exe`). Either rename it to `lua.exe` or call it by its full name.

---

## 1.3 Installing Lua on macOS

The easiest way is [Homebrew](https://brew.sh). With Homebrew installed, run:

```text
brew install lua
```

Homebrew installs the current stable Lua release (5.4.x at the time of writing). If you use MacPorts instead, run `sudo port install lua`.

macOS does not include Lua by default, so you will not find it on a fresh system.

---

## 1.4 Installing Lua on Linux

Most distributions package Lua. Use the command for your distribution:

```text
# Debian, Ubuntu, Linux Mint
sudo apt update
sudo apt install lua5.4

# Fedora
sudo dnf install lua

# Arch Linux
sudo pacman -S lua

# openSUSE
sudo zypper install lua54
```

On Debian-based systems the executable is installed as `lua5.4`, and a `lua` command may or may not exist. If `lua` is missing, run your scripts with `lua5.4` or create an alias:

```text
alias lua=lua5.4
```

> **Note:** Distribution packages sometimes lag behind the newest release. If you need an exact version, build from source (1.5).

---

## 1.5 Building Lua from Source

Building from source works on any system with a C compiler and `make`, and it always gives you the newest release. The commands below follow the instructions on the official download page (<https://www.lua.org/download.html>). Replace `5.4.7` with the latest 5.4.x version listed there.

```text
curl -L -R -O https://www.lua.org/ftp/lua-5.4.7.tar.gz
tar zxf lua-5.4.7.tar.gz
cd lua-5.4.7
make all test
sudo make install
```

What each step does:

| Command | Purpose |
|---|---|
| `curl ...` | Downloads the source archive |
| `tar zxf ...` | Unpacks it |
| `make all test` | Compiles Lua and runs a quick self-test |
| `sudo make install` | Copies `lua` and `luac` into `/usr/local/bin` |

If the build fails on Linux complaining about `readline`, install the development headers first (on Debian/Ubuntu: `sudo apt install build-essential libreadline-dev`). On Windows you can build with MinGW or Visual Studio, following the notes in the `README` inside the source folder.

---

## 1.6 Verifying Your Installation (`lua -v`)

Open a **new** terminal (so it picks up any `PATH` changes) and run:

```text
lua -v
```

You should see something like:

```text
Lua 5.4.7  Copyright (C) 1994-2024 Lua.org, PUC-Rio
```

The exact numbers depend on your version. If you see "command not found" (or "is not recognized as an internal or external command"), the interpreter is either not installed or not on your `PATH`. On Debian-based systems try `lua5.4 -v`.

For a first real test, run a one-liner:

```text
lua -e "print('Hello, Lua!')"
```

Output:

```text
Hello, Lua!
```

You can also check the version from inside Lua through the built-in `_VERSION` variable:

```lua
print(_VERSION)  --> Lua 5.4
```

---

## 1.7 Lua vs LuaJIT vs Luau: Which One Do You Need?

"Lua" is a language, and there are several implementations and dialects. They share most syntax but differ in features and speed.

| Name | What it is | Based on | When to use it |
|---|---|---|---|
| **PUC-Rio Lua** (this course) | The reference implementation from lua.org | 5.4 is current | Learning, general scripting, embedding |
| **LuaJIT** | A very fast just-in-time compiler with a C FFI | Lua 5.1 plus extensions | Performance-critical code; some apps (OpenResty, many Neovim setups) |
| **Luau** | Roblox's fork with gradual typing and extra syntax | Lua 5.1 lineage | Roblox development |

Practical advice:

- **Learning the language?** Install standard Lua 5.4. Everything here applies directly.
- **Targeting a specific host** such as Neovim, Roblox, or LÖVE? Learn the core language first; the host's documentation tells you which Lua variant it uses. Neovim embeds LuaJIT (or Lua 5.1), LÖVE uses LuaJIT, and Roblox uses Luau.
- Throughout this course, differences between versions are marked in notes. Lesson 27 collects them all in one place.

---

## 1.8 Choosing a Code Editor / IDE (VS Code + Lua Language Server, ZeroBrane, Neovim)

Any text editor can edit Lua, but editors with Lua support give you syntax highlighting, autocompletion, and error warnings as you type.

**Visual Studio Code + Lua Language Server (recommended for beginners).**
1. Install [VS Code](https://code.visualstudio.com).
2. Open the Extensions panel and install the **Lua** extension by *sumneko* (it bundles the Lua Language Server, "LuaLS").
3. Open a folder, create a file called `hello.lua`, and start typing. You get completion, hover docs, and warnings (for example, about accidental global variables).

**ZeroBrane Studio.** A lightweight IDE written for Lua. It has a built-in debugger and a REPL panel, and it works with several Lua variants out of the box. Good if you want an all-in-one tool.

**Neovim.** If you already use Neovim, configure the `lua_ls` language server (through the built-in LSP client and the `nvim-lspconfig` plugin). Neovim also uses Lua as its configuration language, which is covered in Lesson 38.

**Others.** JetBrains IDEs have a Lua plugin, and Sublime Text, Vim, and Emacs all have Lua modes.

### A first script

Whatever editor you choose, create a file named `hello.lua` with this content:

```lua
print("Hello, Lua!")
print("2 + 3 =", 2 + 3)
```

Run it from a terminal opened in the same folder:

```text
lua hello.lua
```

Output:

```text
Hello, Lua!
2 + 3 =	5
```

(The gap before `5` is a tab character: `print` separates multiple arguments with tabs.) Lesson 2 explains all the ways to run code.

---

[Previous](./[0]-Introduction-to-Lua.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[2]-Running-Lua-Code.md)
