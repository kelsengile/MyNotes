[Previous](./[2]-Running-Ruby-Code.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[4]-Ruby-Basics.md)

*Getting Started*

# Lesson 3 - Your Development Environment (editors, Bundler, RuboCop, debugger)

A comfortable setup makes learning faster. This lesson covers editors, editor intelligence, automatic style checking, dependency management, and how to lay out a project.

---

## 3.1 Choosing an Editor or IDE

Ruby is just text, so almost any editor works. Popular choices:

| Editor | Notes |
|--------|-------|
| **Visual Studio Code** | Free, huge extension ecosystem, great default for beginners |
| **RubyMine** | Full IDE from JetBrains (paid, with free options for learning). Excellent refactoring and debugging out of the box |
| **Neovim / Vim** | Fast and keyboard-driven; needs plugin setup |
| **Zed** | Modern, fast editor with Ruby support |
| **Sublime Text** | Lightweight and fast |
| **Emacs** | Highly customizable, with `ruby-mode` |

What to look for: syntax highlighting, auto-indent (Ruby convention is **2 spaces**), code completion, go-to-definition, an integrated terminal, and debugger support.

---

## 3.2 Ruby Language Servers and Extensions

A **language server** (using the Language Server Protocol, LSP) is a background program that gives *any* editor smart features: completion, hover documentation, go-to-definition, find references, rename, and diagnostics.

| Tool | Description |
|------|-------------|
| **Ruby LSP** | Language server maintained by Shopify. Works with VS Code (extension named "Ruby LSP"), Neovim, Zed, and others. A good default |
| **Solargraph** | Long-standing language server with documentation and type hints |
| **Steep** | Type checker used with RBS signatures (Lesson 74) |

Typical VS Code setup:

1. Install the **Ruby LSP** extension.
2. Open your project folder. The extension installs what it needs (it uses your `Gemfile` if one exists).
3. Optionally add the **debug** extension support (see below) and a **RuboCop** integration.

### The debugger

Ruby ships with the **`debug`** gem. It adds breakpoints and step-through debugging:

```ruby
def total(prices)
  sum = 0
  prices.each { |price| sum += price }
  binding.break      # execution pauses here (you can also write: debugger)
  sum
end

total([5, 10, 15])
```

Run with `ruby file.rb` and you get an interactive prompt where you can print variables (`p sum`), step (`step`, `next`), continue (`continue`), and quit. In VS Code, the **VS Code rdbg Ruby Debugger** extension lets you set breakpoints by clicking in the gutter. A full debugging lesson comes later (Lesson 71).

---

## 3.3 Linting and Formatting with RuboCop

**RuboCop** is the standard Ruby *linter* (it finds style problems and likely bugs) and *formatter* (it can fix many of them automatically). It enforces the community Ruby Style Guide.

```bash
gem install rubocop
rubocop                 # check every Ruby file in the folder
rubocop file.rb         # check one file
rubocop -a file.rb      # safe auto-correct
rubocop -A file.rb      # auto-correct including riskier changes (review the diff!)
rubocop --auto-gen-config   # create a to-do file to adopt RuboCop on an existing project gradually
```

Sample output:

```
file.rb:3:1: C: Style/StringLiterals: Prefer single-quoted strings when you don't need string interpolation
```

Configure it with a `.rubocop.yml` file in your project root:

```yaml
AllCops:
  NewCops: enable
  TargetRubyVersion: 3.4

Style/StringLiterals:
  EnforcedStyle: double_quotes

Layout/LineLength:
  Max: 100
```

Tips:

- You can silence a rule for one line with a comment: `# rubocop:disable Style/Foo`.
- Many editors run RuboCop on save so you see problems immediately. Ruby LSP can run it for you.
- Treat RuboCop as a teacher at first. Read the cop's name (e.g. `Style/StringLiterals`) and look it up in the docs to understand *why*.

Other tools you may meet: **Standard** (RuboCop with fixed, argument-free rules) and **Prettier for Ruby**.

---

## 3.4 Bundler and the Gemfile at a Glance

**Gems** are packages of Ruby code. **Bundler** (installed with Ruby) records which gems and versions your project needs so everyone on the team, and every server, gets the same ones.

```bash
bundle init          # creates a starter Gemfile
```

A `Gemfile`:

```ruby
source "https://rubygems.org"

ruby "~> 3.4"

gem "rake"
gem "json"

group :development, :test do
  gem "rubocop", require: false
  gem "debug"
end
```

```bash
bundle install       # installs gems and writes Gemfile.lock
bundle exec rubocop  # runs a command using exactly the locked gem versions
bundle update        # upgrades gems within your version constraints
bundle add faker     # add a gem to the Gemfile and install it
```

Key ideas:

| File / command | Purpose |
|----------------|---------|
| `Gemfile` | The list of gems **you** want, with optional version constraints |
| `Gemfile.lock` | The exact versions that were installed. Commit it to version control for applications |
| `bundle exec` | Run a command with only the gems in your Gemfile |
| `require "bundler/setup"` | Do the same thing inside a script |

Bundler is covered in depth in Lesson 43. For now, just know it exists and what it is for.

---

## 3.5 A Recommended Project Layout

For small programs, one `.rb` file is fine. As soon as you have more than a couple of files, this layout is the common convention:

```
my_project/
├── bin/                 # executable scripts (e.g. bin/my_tool)
├── lib/                 # your application code
│   ├── my_project.rb    # main file that requires the others
│   └── my_project/
│       ├── calculator.rb
│       └── version.rb
├── test/                # tests (Minitest). Use spec/ for RSpec
│   └── calculator_test.rb
├── .gitignore
├── .rubocop.yml         # style configuration
├── .ruby-version        # Ruby version for rbenv/asdf/mise
├── Gemfile
├── Gemfile.lock
├── Rakefile             # task automation
└── README.md
```

Naming conventions that go along with it:

- **File names** are `snake_case.rb`, and usually match the class they contain (`class ShoppingCart` goes in `shopping_cart.rb`).
- **Folders** under `lib/` mirror module names (`MyProject::Calculator` lives at `lib/my_project/calculator.rb`).
- Put the entry point in `bin/`, reusable code in `lib/`, and tests next to it in `test/` or `spec/`.

A tiny example of the pieces working together:

```ruby
# lib/my_project/calculator.rb
module MyProject
  class Calculator
    def add(a, b) = a + b
  end
end
```

```ruby
# bin/my_tool
#!/usr/bin/env ruby
require_relative "../lib/my_project/calculator"

puts MyProject::Calculator.new.add(2, 3)
```

You can generate a full gem-shaped skeleton with `bundle gem my_project`, which is covered in Lesson 45.

---

[Previous](./[2]-Running-Ruby-Code.md) | [Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[4]-Ruby-Basics.md)