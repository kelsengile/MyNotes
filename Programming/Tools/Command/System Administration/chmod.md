[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# chmod (change mode)

`chmod` changes the permissions on a file or directory — controlling who can read, write, or execute it.

Download: [https://man7.org/linux/man-pages/man1/chmod.1.html](https://man7.org/linux/man-pages/man1/chmod.1.html)

---

## What Is chmod?

Every file on a Unix-like system has permissions for three groups: the **owner**, the **group**, and **everyone else**, each of which can independently have **r**ead, **w**rite, and e**x**ecute permission. `chmod` is how you change those permissions. For a directory specifically, read permission lets you list its contents, write permission lets you create/delete files inside it, and execute permission lets you actually enter (`cd` into) it — a directory that's readable but not executable will show its file names but not let you access them.

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

## Symbolic Notation

```bash
chmod u+x script.sh        # Add execute for the owner (user) only
chmod g-w file.txt          # Remove write for the group
chmod o=r file.txt          # Set others' permission to exactly read-only
chmod a+r file.txt          # Add read for all (user, group, other)
chmod u+x,g+x,o-x file.sh   # Combine multiple changes in one command
```

Symbolic notation (`u`/`g`/`o`/`a` for who, `+`/`-`/`=` for add/remove/set-exactly, `r`/`w`/`x` for what) is more readable than octal for making a small, targeted change without needing to compute the full three-digit permission set from scratch.

---

## Special Permission Bits

```bash
chmod u+s program            # Set the setuid bit — program runs with owner's privileges
chmod g+s folder/             # Set the setgid bit — new files inherit the folder's group
chmod +t folder/               # Set the sticky bit — only file owners can delete their own files
```

These are less commonly needed but important in specific contexts: the sticky bit (commonly set on `/tmp`, mode `1777`) is what prevents one user from deleting another user's files in a shared, world-writable directory.

---

## Recursive Permission Changes

```bash
chmod -R 755 project/
find project/ -type f -exec chmod 644 {} \;
find project/ -type d -exec chmod 755 {} \;
```

A flat `chmod -R 755` applies the exact same permission to both files and directories, which usually isn't quite right — the `find`-based pattern above is the more correct approach for a mixed tree, since it's common to want directories executable (so they can be entered) while regular files are not.

---

## Viewing Current Permissions

```bash
ls -l file.txt
# -rwxr-xr-- 1 alice staff 220 Jun 1 10:00 file.txt
```

The leading 10-character string from `ls -l` breaks down as: file type (`-` for regular file, `d` for directory), then three groups of `rwx` for owner, group, and others — reading this string is the fastest way to sanity-check a `chmod` command's effect.

---

## Common Gotchas

- Forgetting execute on scripts: a freshly created or downloaded script almost never has execute permission by default, producing a "permission denied" error until `chmod +x` is run.
- Overly permissive recursive changes: `chmod -R 777` on a whole directory is a common but dangerous shortcut, since it makes every file world-writable — narrower, targeted changes are almost always the better choice.

---

## Example Walkthrough

```bash
chmod +x deploy.sh
./deploy.sh
```

Adds execute permission to a shell script that was just downloaded or created, since scripts aren't executable by default — a step almost every new script needs before it can be run directly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/System Administration/chown.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# chown (change owner)

`chown` changes the user and/or group that owns a file or directory.

Download: [https://man7.org/linux/man-pages/man1/chown.1.html](https://man7.org/linux/man-pages/man1/chown.1.html)

---

## What Is chown?

Every file on a Unix-like system is associated with exactly one owning user and one owning group, which together with `chmod`'s permission bits determine who can do what with it. `chown` changes that ownership — commonly needed after copying files as root, restoring a backup, or handing off a project directory to a different user or service account. Changing ownership (unlike changing permissions) normally requires root/administrator privileges, since arbitrarily giving away or taking ownership of files would otherwise be a security risk.

---

## Core Commands

```bash
sudo chown alice file.txt              # Change the owning user
sudo chown alice:staff file.txt         # Change both owning user and group
sudo chown :staff file.txt              # Change only the group
sudo chown -R alice:staff folder/       # Recursively change ownership of a folder
sudo chown --from=root alice file.txt   # Only change ownership if currently owned by root
```

---

## User vs Group Ownership

```bash
chown alice file.txt        # Owner becomes alice, group unchanged
chown :developers file.txt   # Group becomes developers, owner unchanged
chown alice:developers file.txt   # Both owner and group set at once
```

The colon separates user from group in the combined form; omitting the part before or after the colon leaves that half unchanged.

---

## Recursive Ownership Changes

```bash
sudo chown -R www-data:www-data /var/www/mysite
```

A very common real-world pattern: after deploying a website's files (often extracted or copied as root), ownership needs to be handed to the actual web server's user/group (`www-data` on many Debian-based systems) so the server process can read and write the files it needs to.

---

## Changing Ownership Following Symlinks

```bash
chown -h alice symlink.txt      # Change the symlink's own owner, not its target
chown -L alice symlink.txt      # Follow the symlink and change the target's owner instead
```

By default, `chown` on a symlink changes the ownership of the file the link points to, not the link itself — `-h` changes this to affect the link's own ownership metadata instead, which matters on systems where symlink ownership is meaningful.

---

## chown vs chgrp

```bash
chgrp developers file.txt      # Equivalent to: chown :developers file.txt
```

`chgrp` is a narrower, dedicated command for changing only group ownership — functionally a shortcut for the group-only form of `chown`, provided as its own tool mostly for historical reasons and scripting clarity.

---

## Common Gotchas

- Permission required: regular users typically cannot `chown` a file to a different user (even one they belong to as a group) without root privileges — this is different from `chmod`, which a file's owner can freely change on their own files.
- Recursive changes on large trees: `chown -R` on a very large directory tree (like a whole web server's document root) can take noticeable time and briefly lock file access during the change.

---

## Example Walkthrough

```bash
sudo chown -R deploy:deploy /srv/app
sudo chmod -R 750 /srv/app
```

Hands over ownership of an application directory to a dedicated deployment user and group, then locks down permissions so only that user and group can access it — a typical pattern when setting up a service to run under its own restricted account.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)