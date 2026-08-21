[Previous](./[3]-Scripting-Environment-Setup.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[5]-Bash-Syntax-and-Variables.md)

*Shell Scripting Fundamentals*

# Lesson 4 - Introduction To The Command Line

## 4.1 What Is a Shell?

A **shell** is a program that reads commands you type and asks the operating system to run them. Bash (Bourne Again SHell) is the most common shell on Linux and macOS. When you open a "terminal," you're talking to a shell.

---

## 4.2 Basic Navigation

```bash
pwd          # print working directory
ls -la       # list files, including hidden ones, in long format
cd path/to/dir   # change directory
cd ..        # move up one directory
cd ~         # go to home directory
```

---

## 4.3 Working With Files

```bash
touch file.txt          # create an empty file
mkdir new_folder         # create a directory
cp file.txt copy.txt     # copy a file
mv file.txt renamed.txt  # move or rename a file
rm file.txt              # delete a file
rm -r folder/            # delete a folder recursively
cat file.txt              # print file contents
```

---

## 4.4 Getting Help

Almost every command has a manual page:

```bash
man ls        # full manual for the ls command
ls --help     # quick summary of options
```

Learning to read `man` pages is one of the most useful command-line skills — it means you rarely need to search the web for basic option syntax.

---

[Previous](./[3]-Scripting-Environment-Setup.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[5]-Bash-Syntax-and-Variables.md)
