[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tmux (terminal multiplexer)

tmux lets you run multiple terminal sessions inside a single window, split panes, and — crucially — keep those sessions running in the background even after you disconnect, letting you reattach to exactly where you left off.

Download: [https://github.com/tmux/tmux](https://github.com/tmux/tmux)

---

## What Is tmux?

A terminal multiplexer sits between you and your actual shell processes, managing one or more independent sessions that keep running regardless of whether a terminal window is open. This solves a very common real-world problem: an SSH connection that drops (or a laptop that goes to sleep) would normally kill any long-running command in that terminal — inside tmux, the session (and everything running in it) keeps going on the remote server, and reconnecting just means reattaching to the still-alive session.

---

## Core Concepts: Sessions, Windows, and Panes

- A **session** is a persistent tmux instance you can detach from and reattach to later.
- A **window** is like a tab within a session — a full-screen area that can be switched between.
- A **pane** is a subdivision of a window, splitting it into multiple visible sections side by side.

This three-level structure (session → windows → panes) is what lets tmux organize dozens of running commands in a way plain multiple terminal windows can't persist across a disconnect.

---

## Core Commands (Outside tmux)

```bash
tmux                       # Start a new, unnamed session
tmux new -s work            # Start a new named session called "work"
tmux ls                      # List all running sessions
tmux attach -t work           # Reattach to a named session
tmux kill-session -t work      # Terminate a named session
```

---

## The Prefix Key

Almost every tmux command inside a session starts with a **prefix key** (`Ctrl+b` by default) followed by another key — this two-step pattern avoids conflicting with normal keyboard shortcuts used by whatever program is running inside a pane.

```
Ctrl+b d       Detach from the current session (it keeps running in the background)
Ctrl+b c       Create a new window
Ctrl+b n       Next window
Ctrl+b p       Previous window
Ctrl+b %       Split the current pane vertically
Ctrl+b "       Split the current pane horizontally
Ctrl+b arrow   Move between panes
Ctrl+b x       Close the current pane
```

---

## Detaching and Reattaching

```
Ctrl+b d
```

This is tmux's signature move: detaching leaves everything running exactly as it was — including any long-running build, server process, or SSH connection inside the session — and returns you to your normal shell. Reattaching later (`tmux attach`) brings the exact same layout and running processes right back.

---

## Working With Panes

```
Ctrl+b %          Split vertically (side by side)
Ctrl+b "          Split horizontally (stacked)
Ctrl+b ←→↑↓       Move between panes
Ctrl+b z          Zoom the current pane to fill the window temporarily
Ctrl+b x           Close the current pane
```

Splitting panes is useful for keeping an editor, a running server, and a log tail all visible simultaneously in one terminal window, without needing several separate terminal applications.

---

## Managing Multiple Windows

```
Ctrl+b c          Create a new window
Ctrl+b 0-9        Jump directly to window number 0-9
Ctrl+b w          Show a list of all windows to choose from
Ctrl+b ,           Rename the current window
```

---

## Copy Mode (Scrolling Back)

```
Ctrl+b [          Enter copy/scroll mode
q                  Exit copy mode
```

Because tmux takes over the terminal's normal scrollback behavior, `Ctrl+b [` is needed to scroll back through a pane's history — plain mouse-wheel scrolling or `Shift+PageUp` may not work as expected without this, depending on terminal and configuration.

---

## Configuration: .tmux.conf

```
# ~/.tmux.conf
set -g prefix C-a          # Change the prefix key from Ctrl+b to Ctrl+a
set -g mouse on             # Enable mouse support for pane selection/resizing
```

Like most terminal tools, tmux's defaults (especially the prefix key) are commonly remapped to personal preference via `~/.tmux.conf`.

---

## Common Gotchas

- Forgetting the prefix: every command needs `Ctrl+b` first — pressing a shortcut without it usually just sends that keystroke to whatever program is running in the current pane instead.
- Nested tmux sessions: running tmux inside tmux (e.g. over SSH into a machine that's itself inside a local tmux session) makes prefix keys ambiguous unless one session uses a different prefix or the outer session's keystrokes are explicitly passed through.

---

## Example Walkthrough

```bash
tmux new -s deploy
# run a long deployment script
Ctrl+b d
# disconnect from SSH entirely — the deployment keeps running
ssh back-into-server
tmux attach -t deploy
```

Starts a named session for a long-running deployment, detaches (leaving it running) before disconnecting from the server entirely, then reattaches later from a fresh connection to check on progress — the classic reason tmux is indispensable for remote server work.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)