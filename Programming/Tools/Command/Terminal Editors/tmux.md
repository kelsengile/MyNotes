[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# tmux (Terminal Multiplexer)

tmux lets you run multiple terminal sessions, windows, and panes inside a single terminal window — and, crucially, keeps those sessions running even if you disconnect.

Download: [https://github.com/tmux/tmux](https://github.com/tmux/tmux)

---

## What Is tmux?

A "multiplexer" splits one terminal connection into many independent sessions. The standout feature is persistence: start a long-running task inside a tmux session over SSH, disconnect (intentionally or due to a dropped connection), and reconnect later to find it still running exactly where you left it.

---

## Core Commands

```bash
tmux new -s mysession       # Start a new named session
tmux ls                      # List all running sessions
tmux attach -t mysession     # Reattach to an existing session
tmux kill-session -t mysession  # End a session
```

**Inside tmux (after pressing the prefix key, `Ctrl+B` by default):**
```
Ctrl+B  d      Detach from the session (leaves it running in the background)
Ctrl+B  c      Create a new window
Ctrl+B  %      Split the pane vertically
Ctrl+B  "      Split the pane horizontally
Ctrl+B  arrow  Move between panes
```

---

## Why It Matters for Remote Work

```bash
ssh server
tmux new -s build
./long_running_build.sh
# Ctrl+B, then d — detach, close your laptop, go home
ssh server
tmux attach -t build       # Your build is still running, exactly as you left it
```

Without tmux, closing an SSH connection would kill anything running in that session. tmux decouples the running process from the connection itself.

---

## Example Walkthrough

```bash
tmux new -s deploy
./deploy.sh
```

Starts a new named session and kicks off a deployment script inside it — if the SSH connection drops partway through, reconnecting and running `tmux attach -t deploy` picks the session back up mid-deployment.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
