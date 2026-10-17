[Previous](./[0]-Introduction-to-Ruby.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[2]-Syntax-Basics-And-Output.md)

*Basics*

# Lesson 1 - Getting Started With Ruby

## 1.1 What Is Ruby?

Ruby is a dynamic, open-source, general-purpose programming language created by **Yukihiro "Matz" Matsumoto** in Japan. It was first released publicly in 1995 and was designed with a clear goal: make programmers happy and productive. Its syntax reads almost like English, and it is fully **object-oriented**: numbers, strings, `nil`, and even classes are objects.

Key characteristics:

- **Interpreted** - you run source code directly; the reference implementation (CRuby / MRI) compiles it to bytecode for the YARV virtual machine.
- **Dynamically typed** - variables have no declared type; values carry their type.
- **Garbage collected** - memory is managed for you.
- **Multi-paradigm** - object-oriented at the core, with strong functional features (blocks, lambdas, `map`/`select`/`reduce`).
- **Expressive and flexible** - almost everything is an expression that returns a value, and you can reopen classes and add behavior at runtime.

Where Ruby is used: web development (Ruby on Rails, Sinatra), scripting and automation, DevOps tools (Chef, Vagrant, Homebrew), testing (RSpec, Cucumber), prototypes, and data-processing tasks.

Ruby has a regular release cycle: a new minor version arrives every Christmas Day (December 25). **Ruby 4.0** was released on December 25, 2025, on the language's 30th anniversary. It introduced `Ruby::Box` (an experimental feature for isolating definitions), the experimental `ZJIT` compiler, and an improved `Ractor` API. This course targets Ruby 4.0, and notes where older versions behave differently.

---

## 1.2 Installing Ruby

First check whether Ruby is already installed:

```bash
ruby -v
```

Example output: `ruby 4.0.0 (2025-12-25 revision ...) +PRISM [x86_64-linux]`

| Platform | Recommended approach |
| --- | --- |
| Windows | Use **RubyInstaller** (rubyinstaller.org); it includes the MSYS2 toolchain needed to build native gems. WSL2 with the Linux instructions also works well. |
| macOS | Install with Homebrew (`brew install ruby`) or use a version manager. The Ruby bundled with macOS is old; do not use it for development. |
| Linux | Use the package manager (`sudo apt install ruby-full`) for convenience, or a version manager for the latest release. |

**Version managers** let you install several Ruby versions side by side and switch per project:

- **rbenv** (with `ruby-build`) - lightweight; uses shims and a `.ruby-version` file.
- **RVM** - feature-rich; can also manage gemsets.
- **asdf** / **mise** - general-purpose tools that handle Ruby along with other languages.
- **chruby** - minimal; switches Ruby by changing `PATH`.

Example with rbenv:

```bash
rbenv install 4.0.0
rbenv global 4.0.0
rbenv local 4.0.0     # writes .ruby-version in the current folder
ruby -v
```

You can download official source and installers from the Ruby website (see the download link in the Introduction).

---

## 1.3 Running Ruby Code

There are four common ways to run Ruby.

**1. Interactive Ruby (`irb`)** - a REPL (read-eval-print loop) for experimenting:

```bash
irb
```

```ruby
irb(main):001> 2 + 3
=> 5
irb(main):002> "ruby".upcase
=> "RUBY"
```

Type `exit` or press `Ctrl+D` to leave.

**2. Script files** - save code in a `.rb` file and run it:

```bash
ruby hello.rb
```

**3. One-liners** with `-e`:

```bash
ruby -e 'puts "Hello from the command line"'
```

**4. Executable scripts** on Unix-like systems using a *shebang* line:

```ruby
#!/usr/bin/env ruby
puts "I am executable"
```

```bash
chmod +x script.rb
./script.rb
```

Useful command-line flags:

| Flag | Meaning |
| --- | --- |
| `-v` | Print the version and enable verbose warnings |
| `-w` | Turn on warnings |
| `-c` | Check syntax only |
| `-r lib` | Require a library before running |
| `-I dir` | Add a directory to the load path |
| `--yjit` | Enable the YJIT compiler |

---

## 1.4 Your First Program

Create a file named `hello.rb`:

```ruby
puts "Hello, World!"
```

Run it with `ruby hello.rb`. The output is:

```
Hello, World!
```

A slightly bigger example:

```ruby
name = "Ruby"
puts "Hello, #{name}!"
puts "Your name has #{name.length} letters."
3.times { |i| puts "Count: #{i + 1}" }
```

Things to notice:

- No `main` function, semicolons, or type declarations are needed.
- `puts` prints a value followed by a newline.
- `#{...}` inserts the result of any Ruby expression into a double-quoted string.
- `3.times { ... }` calls a method on the integer `3` and passes it a block of code.

---

## 1.5 The Ruby Toolchain And Getting Help

When you install Ruby you also get these command-line tools:

| Tool | Purpose |
| --- | --- |
| `ruby` | The interpreter |
| `irb` | Interactive console |
| `gem` | Package manager for RubyGems (see Lesson 19) |
| `bundle` | Bundler, which manages a project's gem dependencies |
| `rake` | Task runner (Ruby's version of `make`) |
| `ri` | Offline documentation viewer |
| `rdoc` | Documentation generator |

Looking up documentation:

```bash
ri String#upcase
ri Array
```

You can also ask Ruby itself, inside `irb` or a script:

```ruby
"hello".class                      #=> String
"hello".methods.sort.first(5)      #=> a list of method names
"hello".respond_to?(:upcase)       #=> true
```

Online references: the official docs at `docs.ruby-lang.org`, the core and standard library reference at `ruby-doc.org`, and the community "Ruby Style Guide" for conventions.

---

[Previous](./[0]-Introduction-to-Ruby.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[2]-Syntax-Basics-And-Output.md)
