[Previous](./[2]-Running-Lua-Code.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[4]-Variables-And-Data-Types.md)

*Getting Started*

# Lesson 3 - Package Management with LuaRocks

Real projects rarely start from nothing. Libraries for JSON, HTTP, testing, and more are already written by the community. **LuaRocks** is Lua's package manager, the tool that downloads and installs these libraries (called **rocks**).

---

## 3.1 Why Package Management Matters

Lua's standard library is deliberately small. It has no JSON parser, no HTTP client, and no unit-test framework. Instead of copying files by hand, a package manager:

- Finds and downloads libraries from a central repository (<https://luarocks.org>).
- Installs the **dependencies** each library needs, automatically.
- Tracks versions so you can upgrade or remove packages cleanly.
- Builds C extensions for you when a package includes C code.
- Gives you a standard way to **publish** your own libraries.

LuaRocks is to Lua what `pip` is to Python or `npm` is to JavaScript.

---

## 3.2 Installing LuaRocks

LuaRocks is a separate program from Lua itself. Install it with a package manager:

```text
# Debian / Ubuntu
sudo apt install luarocks

# Fedora
sudo dnf install luarocks

# Arch Linux
sudo pacman -S luarocks

# macOS (Homebrew)
brew install luarocks
```

On **Windows**, follow the instructions on the LuaRocks wiki (<https://github.com/luarocks/luarocks/wiki/Installation-instructions-for-Windows>), or use WSL.

You can also install from source from <https://luarocks.org>: download the tarball, then run `./configure && make && sudo make install`.

Verify:

```text
luarocks --version
```

> **Note:** LuaRocks needs to know which Lua version to build for. Rocks are installed per Lua version (5.1, 5.3, 5.4, LuaJIT, and so on). If you have several versions installed, use `luarocks --lua-version 5.4 ...`.

---

## 3.3 Installing, Updating, and Removing Rocks

The core commands:

```text
luarocks search json              # find rocks matching "json"
luarocks install dkjson           # install the latest version of a rock
luarocks install dkjson 2.5       # install a specific version
luarocks list                     # list installed rocks
luarocks show dkjson              # details about an installed rock
luarocks remove dkjson            # uninstall
```

**Updating.** Running `luarocks install name` again installs the newest version available. Older versions remain unless you remove them (`luarocks remove name 2.5-1`).

**Dependencies.** If a rock depends on others, LuaRocks fetches them automatically. Dependencies are declared in the rock's `.rockspec` file (see 3.5).

**Using a rock.** After installation you load the library with `require`:

```lua
local json = require("dkjson")
print(json.encode({ 1, 2, 3 }))   --> [1,2,3]
```

(This sample requires `dkjson` to be installed first.)

---

## 3.4 Local vs Global Installs (`--local`, `--tree`)

By default, `luarocks install` writes into a **system-wide** location, which usually needs administrator rights (`sudo`). You have two other choices.

**Per-user install (`--local`).** Installs into a folder in your home directory (usually `~/.luarocks`), no `sudo` needed:

```text
luarocks install --local dkjson
```

**Project-local tree (`--tree`).** Installs into any folder you choose, which keeps each project's dependencies separate:

```text
luarocks install --tree ./lua_modules dkjson
```

For `--local` and `--tree` installs, Lua must be told where to look for the modules. LuaRocks can print the right settings for your shell:

```text
luarocks path
```

On Linux/macOS, apply them with:

```text
eval "$(luarocks path)"
```

Add that line to your shell startup file (such as `~/.bashrc`) to make it permanent. For a project tree, use `eval "$(luarocks --tree ./lua_modules path)"`.

| Install type | Command flag | Location | Needs admin? |
|---|---|---|---|
| System-wide | (none) | e.g. `/usr/local` | Usually yes |
| Per-user | `--local` | `~/.luarocks` | No |
| Per-project | `--tree <dir>` | Folder you pick | No |

Newer LuaRocks versions also offer `luarocks init`, which sets up a project with a local `lua_modules` tree and a wrapper script.

---

## 3.5 Writing a `.rockspec` File

A **rockspec** describes a package: its name, version, source, dependencies, and how to build it. A rockspec is itself a Lua file, which is why it uses Lua syntax. Here is a minimal one for a library called `greeter`:

```lua
-- greeter-0.1-1.rockspec
package = "greeter"
version = "0.1-1"

source = {
  url = "git+https://github.com/yourname/greeter.git",
  tag = "v0.1",
}

description = {
  summary  = "A tiny greeting library",
  detailed = "Shows how to package a Lua module.",
  homepage = "https://github.com/yourname/greeter",
  license  = "MIT",
}

dependencies = {
  "lua >= 5.1",
}

build = {
  type = "builtin",
  modules = {
    greeter = "src/greeter.lua",
  },
}
```

Key points:

- The **file name** is `<package>-<version>.rockspec`, matching the `package` and `version` fields. The version has two parts: the library version (`0.1`) and the rockspec revision (`-1`).
- `source.url` tells LuaRocks where to download the code.
- `dependencies` lists other rocks and Lua version requirements.
- `build.modules` maps module names (what you pass to `require`) to files. With `type = "builtin"`, LuaRocks handles both Lua and C files.

Useful commands while developing:

```text
luarocks lint greeter-0.1-1.rockspec    # check the rockspec for mistakes
luarocks make greeter-0.1-1.rockspec    # build and install from the current folder
luarocks pack greeter                   # create a distributable archive
```

To share the rock publicly, create an account on luarocks.org and use `luarocks upload`.

---

## 3.6 Using Rocks in Your Scripts (`package.path` / `package.cpath`)

When you call `require("name")`, Lua searches a list of locations. Those lists are stored in two strings:

- `package.path` is for Lua files (`.lua`).
- `package.cpath` is for compiled C libraries (`.so` / `.dll`).

Each is a series of templates separated by semicolons, where `?` is replaced by the module name:

```lua
print(package.path)
--> ./?.lua;/usr/local/share/lua/5.4/?.lua;... (varies by system)
```

If `require` cannot find a module, it raises an error that lists every file it tried, which is very helpful for diagnosis:

```text
module 'dkjson' not found:
    no field package.preload['dkjson']
    no file '/usr/local/share/lua/5.4/dkjson.lua'
    ...
```

Common fixes:

1. **Run `eval "$(luarocks path)"`** before running your script, so the environment variables `LUA_PATH` and `LUA_CPATH` include the rock tree.
2. **Extend the path inside your script** for a local folder of libraries:

```lua
package.path = "./lua_modules/share/lua/5.4/?.lua;" .. package.path
package.cpath = "./lua_modules/lib/lua/5.4/?.so;" .. package.cpath

local ok, json = pcall(require, "dkjson")
print(ok)
```

3. **Check you installed for the right Lua version.** A rock installed for 5.3 is invisible to Lua 5.4.

---

[Previous](./[2]-Running-Lua-Code.md) | [Table of Contents](./[0]-Introduction-to-Lua.md) | [Next](./[4]-Variables-And-Data-Types.md)
