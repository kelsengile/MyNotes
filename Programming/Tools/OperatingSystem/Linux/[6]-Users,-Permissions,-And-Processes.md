[Previous](./[5]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[7]-Using-Linux-Day-To-Day.md)

*System Administration*

# Lesson 6 - Users, Permissions, And Processes

## 6.1 Users And Groups

Linux is a multi-user operating system at its core, even on a personal laptop with only one person using it — every process and file is always associated with a specific user account.

```
$ id alice
uid=1000(alice) gid=1000(alice) groups=1000(alice),27(sudo),999(docker)
```

- **UID (User ID)** — a numeric identifier for a user; UID 0 is always reserved for `root`, the all-powerful administrative account.
- **Groups** — a way of granting permissions to multiple users at once. A user can belong to several groups, and files/directories can grant access based on group membership (Lesson 3.2) rather than listing individual users.
- **`/etc/passwd`** — lists every user account and its basic info (username, UID, home directory, default shell); despite the name, actual passwords are stored securely elsewhere, in `/etc/shadow`.
- **Common commands** — `useradd`/`adduser` (create a user), `usermod -aG docker alice` (add alice to the `docker` group), `groups alice` (list alice's group memberships).

---

## 6.2 sudo And Root Privileges

**`root`** is the Linux administrative account with unrestricted access to the entire system — capable of modifying any file, killing any process, and reconfiguring anything. Operating as root all the time is dangerous, since there's no safety net against a typo (`rm -rf /` as root is catastrophic; the same command as a regular user is usually limited by permissions).

```
$ whoami
alice

$ sudo whoami
[sudo] password for alice:
root
```

- **`sudo` ("superuser do")** — temporarily runs a single command with root privileges, after verifying the requesting user's own password and confirming they're authorized (listed in `/etc/sudoers` or the `sudo` group).
- **Principle of least privilege** — the reasoning behind using `sudo` per-command rather than logging in as root directly: you only elevate privileges for the specific action that needs them, minimizing the blast radius of any mistake.
- **`su`** — switches to another user's shell entirely (`su root` or just `su` starts a root shell after the root password), staying elevated until you explicitly exit; less commonly recommended for routine work than `sudo`.
- **Sudo timeout** — after a successful `sudo` command, most configurations cache that authorization for a few minutes, so repeated `sudo` commands in quick succession don't each re-prompt for a password.

---

## 6.3 Viewing And Managing Processes

Every running program is a **process**, identified by a unique **PID (Process ID)**, viewable and controllable from the command line.

```
$ ps aux | head -5
USER   PID  %CPU  %MEM  COMMAND
alice  1204   0.3   1.2  firefox
alice  1590   2.1   0.8  code
root      1   0.0   0.1  /sbin/init
alice  2201   0.0   0.2  bash
```

```
$ top
```
```
PID   USER   %CPU  %MEM  COMMAND
1590  alice   45.2   3.1  node
1204  alice   12.0   5.4  firefox
```

- **`ps aux`** — a snapshot of all currently running processes, their owning user, and resource usage.
- **`top`** / **`htop`** (an improved, more visual alternative) — a live, continuously updating view of process activity, sortable by CPU or memory usage, useful for spotting a runaway process in real time.
- **`kill`** — sends a signal to a process by PID, most commonly `kill -9 1590` (force-terminate, `SIGKILL`) or the gentler `kill 1590` (`SIGTERM`, asks the process to shut down cleanly first).
- **`&` and `jobs`** — appending `&` to a command runs it in the background of the current shell session, and `jobs` lists background jobs started that way; `fg`/`bg` bring a job to the foreground or resume it in the background.

---

## 6.4 systemd And Services

**systemd** is the init system used by most modern distributions — the very first process started by the kernel at boot (PID 1), responsible for starting all other system services in the correct order and keeping them running.

```
$ systemctl status nginx
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
     Active: active (running) since Mon 2026-09-08 09:12:03 UTC
```

Common `systemctl` operations:

| Command | Effect |
|---|---|
| `systemctl start nginx` | Start a service now |
| `systemctl stop nginx` | Stop a running service |
| `systemctl restart nginx` | Stop then start a service |
| `systemctl enable nginx` | Make a service start automatically on every boot |
| `systemctl disable nginx` | Stop a service from auto-starting on boot |
| `systemctl status nginx` | Show whether it's running and recent log output |

- **Unit files** — plain-text configuration files (usually in `/etc/systemd/system/` or `/lib/systemd/system/`) that define how a service starts, what it depends on, and how it should be restarted if it crashes.
- **`journalctl`** — the companion tool for viewing systemd's centralized logs (e.g. `journalctl -u nginx` shows just that service's log entries), often the first stop when a service fails to start.
- **Why this matters for servers** — nearly every piece of server software you'll manage on Linux (web servers, databases, custom application deployments) is wired up as a systemd service, making `systemctl` one of the most frequently used tools in day-to-day system administration.

[Previous](./[5]-Package-Management.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[7]-Using-Linux-Day-To-Day.md)
