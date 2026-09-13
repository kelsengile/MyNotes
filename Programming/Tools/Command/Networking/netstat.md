[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# netstat

`netstat` displays active network connections, listening ports, and routing and interface statistics. It's available on both Windows and Linux, though the exact flags differ slightly between the two.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/netstat](https://learn.microsoft.com/windows-server/administration/windows-commands/netstat)

---

## What Is netstat?

When a program "can't connect" or you suspect something unexpected is listening on a port, `netstat` shows you every active TCP/UDP connection and every port currently listening for incoming connections, along with which process owns each one.

---

## Core Commands

```bash
netstat -a                # Show all active connections and listening ports
netstat -an                # Same, but show addresses/ports numerically (faster, no DNS lookups)
netstat -ano               # Windows: also show the owning process ID (PID)
netstat -tulnp             # Linux: TCP/UDP listening ports with process names (needs sudo)
netstat -r                 # Show the routing table
```

---

## Finding What's Using a Port

```cmd
netstat -ano | findstr :8080
```

```bash
netstat -tulnp | grep :8080
```

Both find whatever process is bound to port 8080 — useful when a server won't start because "the port is already in use."

---

## Example Walkthrough

```bash
netstat -tulnp | grep LISTEN
```

Lists every port on the machine currently accepting incoming connections, along with the process behind each one — a quick way to audit what's exposed on a server.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
