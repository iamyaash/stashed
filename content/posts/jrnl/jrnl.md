+++
title = 'jrnl: The Smarter, Faster Way to Take Notes on the Go'
date = 2025-04-27T18:28:54+05:30
draft = false
tags = [ "docs", "notes", "jrnl", "usage" ]
summary = "Take notes on the go without distractions, faster and easier than ever. If you're someone who wants to store your notes without opening an application, this versatile and feature-rich solution is perfect for you as you work."
[cover]
image = "posts/jrnl/jrnl.png"
alt = "jrnl-image"
+++

# What is [`jrnl`](https://jrnl.sh/en/stable/)
It is simple yet powerful journal application for the command line which also acts as a note taking application for professionals. It's easy to create, search & view the journal/note entries. Everything is stored in human readable format and it allows encryption too.

It contains almost all the important and fundamental features that you need for taking notes in everyway you want. It is more suitable for those who are familiar with working on the terminal.

# Installation
I'm using **Fedora 41**, so I'll be using the `dnf` package manager to install `jrnl`. You can use the package manager of your own Linux distro, or alternatively, use the `pipx` to install package.

```sh
sudo dnf install jrnl
```
**Alternate & Easiest way to install**
```sh
pipx install jrnl
```

That's it, `jrnl` has been successfully installed in your system.

# Configuration
Open the `jrnl.yaml` in `~/.config/jrnl/` using an editor
```sh
colors:
  body: none
  date: magenta
  tags: yellow
  title: cyan
default_hour: 8
default_minute: 0
editor: /usr/bin/nvim
encrypt: false
highlight: true
indent_character: '|'
journals:
  default: /home/iamyaash/.local/share/jrnl/journal.txt
  fedora: /home/iamyaash/.local/share/jrnl/fedora_journal.txt
linewrap: 88
tagsymbols: '#@'
template: false
timeformat: '%F %T'
version: v4.2
```
It might take some time to get used to work with `jrnl`, so it more advisable to touch the configurations once you are familiar using it.

| Settings              | Description                                                                         |
|-----------------------|-------------------------------------------------------------------------------------|
| `colors`              | Change the colors you want to be displayed in the 4 attributes                      |
| `default_hour/minute` | When the time is not specified it will use the default hour/minute                  |
| `editor`              | Editor of your selection to modify the journal entries                              |
| `encrypt`             | Set to `true` if you need encryption                                                |
| `highlight`           | Just hightlights the essential parts of the entry                                   |
| `indent_character`    | Pretty prints the paragraph                                                         |
| `journals`            | Path of your journal being stored, create multiple if you need add multiple entries |
| `linewrap`            | Wrap the text within certain limit (readability)                                    |
| `tagsymbols`          | Choice of your tagging symbols                                                      |
| `template`            | Advanced but, uses a template to take notes & uses editor for that                  |
| `timeformat`          | Set it 24-hour format                                                               |

# Basic Usage

### **Adding an entry**:

```sh
jrnl "Today is my lucky day!" #heading only
jrnl "Today is my lucky day!" "Because, I got 0 errors during compilation" #heading only
```
```sh
jrnl yesterday at 8pm: I completed working on the bug. Data mismatch was resulting in the bug
#heading + para + time + date

```
> **Note**: Everything until the first sentence mark (`.?!:`) will be interpreted as the title, the rest as the body.

**Output**:

_Note: Uses the time when the entry is added if `time` & `date` are not mentioned._
```sh
iamyaash@fedora:~$ jrnl -n 1
2025-04-27 19:13:08 Today is my lucky day!
| Because, I got 0 errors during compilation
```
```sh
iamyaash@fedora:~$ jrnl -on yesterday
┏━━━━━━━━━━━━━━━━━┓
┃  1 entry found  ┃
┗━━━━━━━━━━━━━━━━━┛
2025-04-26 20:00:00 I completed working on the bug.
| Data mismatch was resulting in the bug
```

### **Viewing & Searching an entry**:
**View**: _Keywords like `on`, `from` & `on` are important and can be used as a flag._
```sh 
jrnl -on yesterday #display only the yesterday entries
jrnl -from yesterday #display entries from yesterday to today(now)
jrnl -n 8 || jrnl -8 #displays the last 8 entries
```

**Search**: _The `-contains` command displays all entries containing the text you enter after it_
```sh
jrnl -contains "meet" -from last-week
jrnl -contains "program" -from last-week --edit
```

### **Editing an entry**:

_Opens the editor of your choice, as mentioned in the `jrnl.yaml` -> `editor` config above. For me, it's nvim.
If you are familiar with **`nvim`**, you can navigate through it easily._

```sh
iamyaash@fedora:~$ jrnl -n 1 --edit
```

### **Deleting an entry**:

_Opens the editor with the specified number of recent entries. For example, use `-n 2` to open the last two entries, allowing you to delete the last entry if needed._

_Note: Even if you delete the entry using `-n 1 --edit`, the contents inside will be deleted but the date & time remains_
```sh
iamyaash@fedora:~$ jrnl -n 2 --edit
```