[Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[2]-Running-Ruby-Code.md)

*Getting Started*

# Lesson 1 - Installing Ruby (Installers, rbenv, RVM, asdf)

Before you can write Ruby, you need the Ruby interpreter on your machine. This lesson explains which version to pick, how to install it on each operating system, how to manage several versions side by side, and how to confirm everything works.

---

## 1.1 Ruby Versions and Implementations

Ruby was created by Yukihiro "Matz" Matsumoto and first released in 1995. Today it is developed in the open and a new minor version is released every year on December 25th.

**Version numbers** look like `MAJOR.MINOR.PATCH`, for example `3.4.1`:

- **Major / minor** releases (`3.3`, `3.4`, `4.0`) add features and may deprecate old behavior.
- **Patch** releases (`3.4.1`, `3.4.2`) only fix bugs and security issues.
- Each minor series is supported for a limited time. Always check the [maintenance branches page](https://www.ruby-lang.org/en/downloads/branches/) and prefer a **currently supported** version.

> **Tip:** For learning, install the latest stable release. For work projects, use the version the project asks for (usually written in a `.ruby-version` file).

**"Ruby" is a language, and there are several programs that can run it.** These are called *implementations*:

| Implementation | Also called | Written in | Best known for |
|----------------|-------------|------------|----------------|
| **CRuby** | MRI, "Matz's Ruby Interpreter" | C (and Ruby/Rust for parts) | The reference implementation. This is what `ruby` means on 99% of machines. |
| **JRuby** | | Java | Runs on the JVM, real parallel threads, Java library access |
| **TruffleRuby** | | Java (GraalVM) | Very high peak performance |
| **mruby** | | C | Small, embeddable Ruby for devices and apps |

This course uses **CRuby**. Everything you learn works on the others unless a lesson says otherwise. Implementations are compared in more detail in Lesson 81.

---

## 1.2 Installing on Windows (RubyInstaller)

There are two good options on Windows.

### Option A: RubyInstaller (native Windows)

1. Go to [rubyinstaller.org/downloads](https://rubyinstaller.org/downloads/).
2. Download the **Ruby+Devkit** version marked "(recommended)". The Devkit is needed because many gems compile C code.
3. Run the installer. Keep "Add Ruby executables to your PATH" ticked.
4. At the end, leave **"Run 'ridk install' to set up MSYS2 and development toolchain"** ticked and click **Finish**.
5. A terminal opens and shows a menu. Press **Enter** to install the default components (MSYS2 base, system update, and the MINGW development toolchain).
6. Open a **new** terminal (Command Prompt or PowerShell) and verify with `ruby -v`.

### Option B: WSL 2 (Windows Subsystem for Linux)

Many Ruby developers, especially those who use Rails, run Ruby inside Linux on Windows:

```powershell
wsl --install
```

Restart if asked, open the Ubuntu terminal, and then follow the Linux instructions in the next section. Ruby tooling generally behaves more like production servers there.

---

## 1.3 Installing on macOS and Linux

### macOS

macOS ships with an old system Ruby that Apple uses internally. **Do not use or modify it for development.** Install your own instead.

```bash
# 1. Install Apple's command line tools (compiler, git, make)
xcode-select --install

# 2. Install Homebrew from https://brew.sh, then:
brew install ruby

# 3. Homebrew prints PATH instructions. Typically something like:
echo 'export PATH="$(brew --prefix ruby)/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Linux

Use your package manager for a quick start:

```bash
# Debian / Ubuntu
sudo apt update
sudo apt install ruby-full build-essential

# Fedora
sudo dnf install ruby ruby-devel @development-tools

# Arch
sudo pacman -S ruby base-devel
```

Distribution packages are often a few versions behind. If you need a newer one, use a version manager (next section).

> **Never** run `sudo gem install ...` against the system Ruby. It can break tools your operating system depends on. A version manager installs Ruby in your home directory so `sudo` is never needed.

---

## 1.4 Version Managers: rbenv, RVM, asdf

A **version manager** lets you install many Ruby versions and switch between them per project. This matters because one project might need Ruby 3.3 and another 3.4.

### rbenv (with ruby-build)

rbenv is lightweight: it puts small "shim" programs first on your `PATH` that forward to the right Ruby.

```bash
# macOS
brew install rbenv ruby-build

# Linux: see https://github.com/rbenv/rbenv for the git-based install

# Add to your shell startup file (~/.zshrc or ~/.bashrc):
eval "$(rbenv init - zsh)"      # use "bash" if you use bash
```

```bash
rbenv install -l                # list installable versions
rbenv install 3.4.1             # install one (pick a current version from the list)
rbenv global 3.4.1              # default for your user
rbenv local 3.3.6               # this folder only (writes a .ruby-version file)
rbenv versions                  # show installed versions
rbenv version                   # show the active one and why
```

On Linux, building Ruby needs development libraries such as OpenSSL, libyaml, and zlib. On Ubuntu: `sudo apt install libssl-dev libyaml-dev zlib1g-dev libreadline-dev libffi-dev`.

### RVM (Ruby Version Manager)

RVM is more feature-rich and changes your shell more deeply. It also supports **gemsets** (separate sets of gems per project).

```bash
\curl -sSL https://get.rvm.io | bash -s stable   # install RVM
rvm install ruby                                 # install the latest Ruby
rvm install 3.3.6                                # install a specific version
rvm use 3.3.6                                    # switch for this shell
rvm --default use 3.3.6                          # set the default
rvm list                                         # list installed versions
```

### asdf

asdf is one tool for managing **many languages** (Ruby, Node, Python, ...) through plugins. Versions are recorded in a `.tool-versions` file.

```bash
asdf plugin add ruby
asdf install ruby 3.4.1
asdf set ruby 3.4.1            # asdf 0.16+. On older asdf, use: asdf global ruby 3.4.1
asdf current ruby
```

### Choosing one

| Tool | Pick it when... |
|------|-----------------|
| **rbenv** | You want something simple and predictable (most common for Ruby-only work) |
| **RVM** | You like all-in-one features and gemsets |
| **asdf** | You manage several languages and want a single tool |

Other tools you may meet: **mise** (a fast asdf-compatible tool), **chruby** with **ruby-install**, and **rv**. They solve the same problem. Pick **one** manager and stick with it, because mixing them causes confusing `PATH` problems.

---

## 1.5 Verifying Your Installation

Open a **new** terminal window (so your `PATH` changes take effect) and run:

```bash
ruby -v        # prints the Ruby version, e.g. ruby 3.4.1 (2024-12-25 ...) [x86_64-linux]
gem -v         # RubyGems, Ruby's package manager
bundle -v      # Bundler, installed with Ruby
irb            # interactive Ruby; type exit to leave
```

Then run your first one-liner:

```bash
ruby -e 'puts "Hello from Ruby!"'
```

Useful diagnostic commands:

```bash
which ruby         # (Windows: where ruby) which executable runs?
gem env            # install paths and settings
gem update --system   # update RubyGems itself
```

### Troubleshooting

| Problem | Likely cause and fix |
|---------|----------------------|
| `ruby: command not found` | Ruby is not on your `PATH`. Open a new terminal, or re-check the shell init line from the install step. |
| Wrong version shown | Another Ruby comes first on `PATH`. Run `which ruby` and fix the order (version-manager shims should be first). |
| `Permission denied` on `gem install` | You are using a system Ruby. Switch to a version manager rather than using `sudo`. |
| Compile errors while installing a Ruby or gem | Missing build tools or libraries. Install a compiler and the development libraries listed above (on Windows, run `ridk install`). |
| `bundle` not found | Run `gem install bundler`. |

---

[Table of Contents](./[0]-Introduction-to-Ruby.md) | [Next](./[2]-Running-Ruby-Code.md)