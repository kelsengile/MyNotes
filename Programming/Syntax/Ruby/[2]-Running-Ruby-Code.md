[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Running Ruby: IRB, Scripts & the REPL

Ruby code can be run in several ways: interactively, from a file, or straight from the command line. This lesson shows each one and explains what Ruby does with your code behind the scenes.

---

## 2.1 Interactive Ruby (IRB)

**IRB** (Interactive Ruby) is a **REPL**: **R**ead a line, **E**valuate it, **P**rint the result, **L**oop. It is the best place to try ideas and explore the language.

```bash
irb
```

```ruby
irb(main):001> 2 + 3
=> 5
irb(main):002> "ruby".upcase
=> "RUBY"
irb(main):003> name = "Ada"
=> "Ada"
irb(main):004> "Hello, #{name}!"
=> "Hello, Ada!"
```

Every expression in Ruby has a value, and IRB prints it after `=>`. Things worth knowing:

| Feature | How to use it |
|---------|---------------|
| Last result | `_` holds the previous result in older IRB versions; newer versions may not, so assign to a variable instead |
| Leave IRB | `exit` or `Ctrl+D` |
| Help | `help` (or `help String#upcase` for documentation of a method) |
| Method list | `ls "text"` shows the methods of an object |
| Show source | `show_source String#upcase` (for methods written in Ruby) |
| Multi-line code | Press Enter inside an unfinished `def`, `if`, or `do` and IRB waits for `end` |
| Autocomplete | Press `Tab` after typing part of a name |
| History | Up/Down arrows; history is saved between sessions |
| Reload a file | `load "my_file.rb"` runs the file again |

Variables you create in IRB stay alive until you exit, so you can build things step by step.

---

## 2.2 Running a Script File

For anything longer than a few lines, save the code in a file. Ruby files conventionally end in `.rb`.

Create `hello.rb`:

```ruby
puts "Hello, Ruby!"
name = "Ada"
puts "Welcome, #{name}."
```

Run it:

```bash
ruby hello.rb
```

Output:

```
Hello, Ruby!
Welcome, Ada.
```

Some useful command-line options:

| Command | What it does |
|---------|--------------|
| `ruby hello.rb` | Run the file |
| `ruby -c hello.rb` | Only **c**heck the syntax (prints `Syntax OK`) |
| `ruby -w hello.rb` | Run with **w**arnings enabled (catches suspicious code) |
| `ruby -r json hello.rb` | **r**equire a library before the script runs |
| `ruby -I lib hello.rb` | Add a folder (`lib`) to the load path |
| `ruby -v` | Print the version (also runs with warnings if given a script) |
| `ruby hello.rb a b` | Pass arguments, available in the `ARGV` array |

A script can read its arguments:

```ruby
# greet.rb
name = ARGV[0] || "stranger"
puts "Hello, #{name}!"
```

```bash
ruby greet.rb Ada     # Hello, Ada!
ruby greet.rb         # Hello, stranger!
```

### The "run only when executed directly" idiom

A file can be both a library (loaded by other code) and a script:

```ruby
def greet(name)
  "Hello, #{name}!"
end

if __FILE__ == $PROGRAM_NAME
  puts greet("Ada")   # only runs when you do: ruby this_file.rb
end
```

`__FILE__` is the name of the current file and `$PROGRAM_NAME` (also `$0`) is the name of the script that was started.

---

## 2.3 Shebang Lines and Executable Scripts

On Linux and macOS you can run a Ruby file like any other program. Put a **shebang** on the very first line, telling the operating system which interpreter to use:

```ruby
#!/usr/bin/env ruby

puts "I am an executable script!"
```

`/usr/bin/env ruby` finds `ruby` wherever it is installed (including version-manager shims), which is more portable than hard-coding a path.

Then make the file executable and run it:

```bash
chmod +x hello.rb
./hello.rb
```

You can also drop the `.rb` extension for command-line tools and place the file in a folder on your `PATH` (such as `~/bin`) so you can run it from anywhere.

> **Windows:** shebang lines are ignored by `cmd` and PowerShell. Run `ruby hello.rb` instead (RubyInstaller also associates `.rb` files with Ruby).

---

## 2.4 One-Liners with ruby -e

The `-e` flag runs Ruby code given directly on the command line, with no file:

```bash
ruby -e 'puts 6 * 7'                       # 42
ruby -e 'puts Time.now'                    # the current time
ruby -e '3.times { |i| puts "Line #{i}" }'
```

Ruby can also act as a powerful text-processing tool like `sed` or `awk`:

| Flag | Meaning |
|------|---------|
| `-n` | Wrap code in a loop that reads each input line into `$_` |
| `-p` | Like `-n`, but also prints `$_` after each pass |
| `-l` | Automatically remove the newline from each line and add it back on output |
| `-a` | Auto-split each line into the array `$F` (with `-n` or `-p`) |
| `-i` | Edit files in place |

```bash
# Print only lines containing "error"
ruby -ne 'puts $_ if $_ =~ /error/' app.log

# Uppercase every line of input
echo "hello" | ruby -pe '$_.upcase!'

# Print the second word of each line
echo "alpha beta gamma" | ruby -lane 'puts $F[1]'     # beta
```

Quoting tip: wrap one-liners in **single quotes** on macOS/Linux. On Windows Command Prompt, use double quotes outside and single quotes inside.

---

## 2.5 How Ruby Interprets Your Code

Ruby is often called an *interpreted* language, but modern Ruby actually does several steps before running your program:

1. **Read and tokenize** the source text.
2. **Parse** it into an **abstract syntax tree (AST)** that represents the structure of the program. Ruby's default parser is called **Prism** (in current versions).
3. **Compile** the AST into **bytecode** (instructions for Ruby's virtual machine, called **YARV**: *Yet Another Ruby VM*).
4. **Execute** the bytecode on the virtual machine. Optionally, a **JIT compiler** such as **YARV's YJIT** turns hot (frequently used) code into native machine code for extra speed (Lesson 78).

You can look at the bytecode yourself:

```ruby
code = RubyVM::InstructionSequence.compile("1 + 2")
puts code.disasm
```

Or from the shell:

```bash
ruby --dump=insns -e 'puts 1 + 2'
```

Important consequences:

- **Syntax errors are found before anything runs.** If a file has a syntax error, none of it executes. Use `ruby -c file.rb` to check.
- **Ruby runs top to bottom.** A method must be *defined* before the line that *calls* it executes.
- **Almost everything happens at runtime.** Classes and methods are created as the program runs, which is what makes metaprogramming possible (Lessons 34 to 40).
- **There is no `main()` function.** The top-level code of the file is the program. The implicit top-level object is called `main`:

```ruby
puts self          # main
puts self.class    # Object
```

- **Errors happen at runtime.** Ruby does not check types before running, so a typo in a method name only fails when that line is reached.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[3]-Development-Environment.md)