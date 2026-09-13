[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# chown (change owner)

`chown` changes which user and/or group owns a file or directory. Ownership, combined with permission bits, is the core of the Unix access-control model — `chown` decides *who* the permission bits apply to.

Reference: [https://man7.org/linux/man-pages/man1/chown.1.html](https://man7.org/linux/man-pages/man1/chown.1.html)

---

## What Is chown?

Every file on a Unix-like system has exactly one owning user and one owning group stored in its inode. Permission bits (`rwx`) are then evaluated in three tiers — owner, group, and "others" — so changing a file's owner or group effectively changes which permission tier applies to which people. Only the superuser (`root`) can give a file away to a different owner; a regular, non-root user generally cannot `chown` a file to someone else, even a file they themselves own, since that would let users dodge disk quotas or dump files on other accounts.

---

## Core Commands

```bash
chown alice file.txt              # Change the owner to user 'alice'
chown alice:staff file.txt        # Change owner to 'alice' and group to 'staff'
chown :staff file.txt             # Change only the group, leave the owner as-is
chown -R alice:staff folder/      # Recursively change ownership of a folder and everything inside
chown --reference=other.txt file.txt  # Copy ownership from another file
chown -v alice file.txt           # Print a confirmation message
sudo chown -R www-data:www-data /var/www/html   # Typical web server ownership fix
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-R` | `--recursive` | Apply to a directory and everything inside it |
| `-v` | `--verbose` | Print each change made |
| `-c` | `--changes` | Like verbose, but only report files actually changed |
| `--reference=FILE` | | Copy ownership settings from another file instead of specifying names |
| `-h` | `--no-dereference` | Change the symlink itself, not the file it points to |
| `--from=OWNER:GROUP` | | Only change ownership if it currently matches this — a safety net during bulk changes |

---

## Owner, Group, and Why Both Matter

```bash
chown alice:developers project/
```

This sets `alice` as the owning user and `developers` as the owning group. Group ownership is what lets multiple unrelated users share access to the same files without making everyone the individual owner — a common pattern for shared project directories, where every team member belongs to a `developers` group with read/write access, regardless of who personally created each file.

---

## chown vs. chgrp vs. chmod

- **`chown`** — changes *who* owns a file (user and/or group).
- **`chgrp`** — changes only the *group* owner; functionally a shortcut for `chown :group`.
- **`chmod`** — changes *what* the owner/group/others are allowed to do (read/write/execute), independent of who they are.

These three tools work together: `chown` decides whose permissions matter, `chmod` decides what those permissions grant.

---

## Common Real-World Uses

- **Fixing "permission denied" after copying files as root** — files extracted from an archive or copied with `sudo` often end up owned by `root`, breaking access for the regular application user; `chown -R appuser:appgroup /path` is the standard fix.
- **Web server directories** — ensuring files served by `nginx`/`apache` are owned by the web server's user (commonly `www-data`, `nginx`, or `apache`) so the server process can read (and sometimes write) them.
- **Docker volume permission mismatches** — files created inside a container often end up owned by a UID that doesn't correspond to any real user on the host, requiring `chown` on the host side to make them accessible again.
- **Home directory recovery** — after restoring a user's home directory from backup, `chown -R username:username /home/username` restores correct per-user ownership.

---

## Common Pitfalls

- **Recursive chown on the wrong path** can silently change ownership of far more than intended (e.g., running it from the wrong working directory) — always double-check the target path, especially with `-R` and `sudo`.
- **Numeric UIDs/GIDs**: `chown` also accepts numeric IDs (`chown 1000:1000 file`), which matters when the corresponding name doesn't exist locally — common with Docker containers or NFS-mounted files from another system.
- **Symlinks**: by default `chown` follows a symlink and changes the ownership of its target; use `-h` to change the link itself.
- **Requires privilege**: attempting `chown someoneelse file.txt` as a non-root user typically fails with "Operation not permitted," which is expected behavior, not a bug.

---

## Related Tools

- `chgrp` — group-only ownership changes.
- `chmod` — permission bit changes.
- `id username` — shows a user's UID, primary group, and all supplementary groups.
- `stat file.txt` — shows a file's current owner, group, and permission bits.
- `setfacl`/`getfacl` — finer-grained Access Control Lists beyond the basic owner/group/other model.

---

## Example Walkthrough

```bash
sudo chown -R deploy:deploy /srv/app
sudo chmod -R 750 /srv/app
```

Reassigns an entire application directory to a dedicated `deploy` user and group, then locks down permissions so only that user and group can read/write/execute the contents — a standard hardening step after deploying an application under its own dedicated service account rather than `root`.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)