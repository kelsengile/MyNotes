[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# Make

Make is one of the oldest and most widely used build automation tools. It reads a `Makefile` describing how to turn source files into a finished program, and only rebuilds the parts that actually changed.

Download: [https://www.gnu.org/software/make/](https://www.gnu.org/software/make/)

---

## What Is Make?

A `Makefile` defines **targets** (things you want to build), the **prerequisites** each target depends on, and the **recipe** (shell commands) used to build it. Make compares file timestamps: if a prerequisite is newer than its target, the recipe runs again; otherwise Make skips it, which is what makes large rebuilds fast.

---

## A Minimal Makefile

```make
program: main.o utils.o
	gcc -o program main.o utils.o

main.o: main.c
	gcc -c main.c

utils.o: utils.c
	gcc -c utils.c

clean:
	rm -f *.o program
```

Recipe lines must start with a literal Tab character, not spaces — this trips up almost everyone the first time.

---

## Core Commands

```bash
make                # Build the first target in the Makefile
make target_name     # Build a specific target
make clean           # Common convention for removing build output
make -j4             # Build using 4 parallel jobs
make -n              # Dry run — print commands without running them
```

---

## Why It Still Matters

Even projects that use modern build systems like CMake or Meson often generate a Makefile as their final output, since `make`'s dependency-tracking model is simple, fast, and available on virtually every Unix-like system by default.

---

## Example Walkthrough

```bash
make
make clean
make -j4
```

Builds the project using the default target, removes the build output, then rebuilds it again using 4 parallel jobs for a faster compile.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
