[Previous](./[7]-System-Information-Commands.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[9]-Networking-Commands.md)

# Lesson 8 - Process and Task Management

## 8.1 Listing running processes

```
tasklist
```
Shows every running process, its Process ID (PID), and how much memory it's using.

Filter it down:
```
tasklist | find "chrome"
```
Shows only processes with "chrome" in the name.

---

## 8.2 Ending a process

```
taskkill /im notepad.exe
```
Ends all processes named `notepad.exe` (`/im` = "image name").

```
taskkill /pid 1234
```
Ends the specific process with PID `1234` (get the PID from `tasklist` first).

If a program won't close normally, force it:
```
taskkill /f /im notepad.exe
```
`/f` forces termination. Use this as a last resort — unsaved work in that program will be lost.

---

## 8.3 Why use CMD instead of Task Manager?

- You can filter and search process lists with `find`.
- You can kill processes by name across a whole fleet of machines via scripts.
- It's scriptable — you can automate "if this program is running, close it" logic in a batch file.

---

## 8.4 Checking if something is running (for use in scripts)

```
tasklist | find /i "spotify.exe" > nul && echo Running || echo Not running
```
This checks for Spotify, throws away the visible output, and just prints Running or Not running based on whether it was found. (The `&&` / `||` syntax is covered more in Lesson 13.)

---

[Previous](./[7]-System-Information-Commands.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[9]-Networking-Commands.md)
