+++
title = 'cheat-sheet: tmux basic usage'
summary = "Cheat Sheet & Quick Reference for tmux"
date = 2025-05-09T16:22:55+05:30
draft = false
tags = ["cheat-sheet", "tmux"]
+++

# What is `tmux`?
tmux is a program which runs in a terminal and allows multiple other terminal programs to be run inside it. Each program inside tmux gets its own terminal managed by tmux, which can be accessed from the single terminal where tmux is running - this called multiplexing and tmux is a terminal multiplexer.

# What is Sessions, Windows, and Panes

* **Sessions**: A session is a container for one or more windows in a terminal multiplexer like `tmux` or `screen`.
* **Windows**: A window is a single terminal screen within a session, which can run its own shell or process.
* **Panes**: A pane is a split view within a window that allows multiple terminal areas to be visible and active simultaneously.

# Sessions

### tmux Session Management Commands

| **Action**               | **Command**                                    | **Notes**                                            |
|--------------------------|------------------------------------------------|------------------------------------------------------|
| Start Session            | `tmux` <br> `tmux new` <br> `tmux new-session` | Starts a new session                                 |
| Kill Current Session     | `:kill-session`                                | Kills the session you're currently in                |
| Kill Specific Session    | `tmux kill-session -t <session-name>`          | Replace `<session-name>` with the target session     |
| Kill All Except Current  | `tmux kill-session -a`                         | Kills all sessions except the one you're attached to |
| Kill All Except Specific | `tmux kill-session -a -t <session-name>`       | Keeps only the specified session                     |
| Detach Session           | `CTRL + b`, then `d`                           | Detach from session and return to shell              |
| Rename Session           | `CTRL + b`, then `$`                           | Rename the current session                           |
| List Sessions            | `tmux ls` <br> `tmux list-sessions`            | Lists all active sessions                            |

# Windows

| **Action**              | **Command**                | **Notes**                  |
|-------------------------|----------------------------|----------------------------|
| Create New Window       | `CTRL + b`, then `c`       | Creates a new window       |
| Rename Current Window   | `CTRL + b`, then `,`       | Renames the current window |
| Close Current Window    | `CTRL + b`, then `&`       | Kills the current window   |
| List Windows            | `CTRL + b`, then `w`       | Shows all windows          |
| Next / Previous Window  | `CTRL + b`, then `n` / `p` | Navigate between windows   |
| Select Window by Number | `CTRL + b`, then `0`–`9`   | Switch to specific window  |

# Panes

| **Action**              | **Command**                                  | **Notes**                                  |
|-------------------------|----------------------------------------------|--------------------------------------------|
| Split Horizontally      | `CTRL + b`, then `%` <br> `:split-window -h` | Horizontal split                           |
| Split Vertically        | `CTRL + b`, then `"` <br> `:split-window -v` | Vertical split                             |
| Switch Panes            | `CTRL + b`, then `Arrow Keys`                | Move between panes using arrow keys        |
| Next Pane               | `CTRL + b`, then `o`                         | Cycles to the next pane                    |
| Swap Panes Left / Right | `CTRL + b`, then `{` or `}`                  | Move pane position within layout           |
| Close Current Pane      | `CTRL + b`, then `x`                         | Closes the pane                            |
| Toggle Layouts          | `CTRL + b`, then `Spacebar`                  | Switch between different pane arrangements |
