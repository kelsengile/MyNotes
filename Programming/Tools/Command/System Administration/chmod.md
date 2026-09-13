[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# chmod (change mode)

`chmod` changes the permissions on a file or directory — controlling who can read, write, or execute it.

Download: [https://man7.org/linux/man-pages/man1/chmod.1.html](https://man7.org/linux/man-pages/man1/chmod.1.html)

---

## What Is chmod?

Every file on a Unix-like system has permissions for three groups: the **owner**, the **group**, and **everyone else**, each of which can independently have **r**ead, **w**rite, and e**x**ecute permission. `chmod` is how you change those permissions.

---

## Core Commands

```bash
chmod +x script.sh          # Add execute permission for everyone
chmod -w file.txt             # Remove write permission for everyone
chmod 755 script.sh           # Set exact permissions using octal notation
chmod 644 file.txt             # Common permissions for a regular, non-executable file
chmod -R 755 folder/            # Apply recursively to a folder and everything inside it
```

---

## Understanding Octal Notation

Each digit represents one group (owner, group, others) as a sum of read (4), write (2), and execute (1):

```
7 = rwx (4+2+1)   5 = r-x (4+1)   6 = rw- (4+2)   4 = r-- (4)
```

So `chmod 755 file` means: owner gets full rwx, group and others get read + execute only. `chmod 644 file` means: owner gets read + write, everyone else gets read-only.

---

## Example Walkthrough

```bash
chmod +x deploy.sh
./deploy.sh
```

Adds execute permission to a shell script that was just downloaded or created, since scripts aren't executable by default — a step almost every new script needs before it can be run directly.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
