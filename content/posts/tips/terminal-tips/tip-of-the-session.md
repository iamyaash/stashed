---
title: 'An Easy Way to Remember Shortcuts'
summary: "I often forget the shortcut keys of editors and other tools. This post explains how I overcame the problem by using a simple trick that makes them easier to remember over time."
date: 2025-06-12T23:12:13+05:30
draft: false
tags:
  - tips
  - productivity
Params:
    ShowReadingTime: true
cover:
  image: posts/tips/terminal-tips/tips.png
  alt: tips
---

# Why are we forgetting the shortcut keys?
People forget keyboard shortcut for several reasons,
1. The **sheer number of keyboard shortcut** available available applications can be overwhelming. It's difficult to keep track of all the different combinations and their respective functions.
2. Many users have developed a preference for **using a mouse**, which is often considered more comfortable and less effortful way to interact with a computer.
3. The keyboard shortcuts are only used in specific tasks or particular applications. If a user **doesn't regularly use a shortcut, it's less likely to be memorized**.
4. The large number of shortcuts and the fact that they are not always used consistently can make it difficult to retain them in memory. It's mostly **comes down to the limitation in memory and learning of the human brain**.

Well, forgetting the shortcut keys are inevitable but we can forget them slowly or maybe it will engrained on your brain by using a simple trick.

# The Solution (not really, but yes it is!)
Do you remember whenever we open certain applications, it will open up a dialog box or a pop up menu that will show you a tip related to the application? (_Corel Draw, GIMP, LibreOffice Tools and so on!_) . Do you also remember certain video games will show you tips on loading screen? (_Skyrim, Assassin's Creed, Wuthering Waves and so on!_) 

Well, there are different reason for it, for instance in video games they just want to make your waiting time a bit insightfull rather than just looking at a plain screen with a progress bar and in applications they actually mean it!

That's the overall idea, and we're going to be diplaying a "_Tip of the Day_" whenever we open a terminal. Maybe it's more like a "_Tip of the Session_"?

## Let's Look At My Side Of The Story 👀
I'm a beginner programmer, Open Source contributor & Linux enthusiast. I mostly use `nvim` for quick edits & `emacs` for works that take longer than 15 minutes. I'm a final year college student, so I usually take some break often times whenever I have upcoming projects or exams and I will eventually come back to my desk at some point within 1 or 2 weeks. 

Whenever I start using `emacs` or `nvim` after a break, I always find myself googling a cheat sheet. I tend to forget the less frequently used shortcuts, which becomes a hassle when I need to do something simple, but end up relying on the mouse instead of keyboard shortcuts, making it slower and frustrating.

Eventually, I switch back to Microsoft VS Code, only to realize I’m being lazy and need to take action. I did some research and found a few solutions, but none of them quite fit my workflow.

One day, while playing Wuthering Waves, I noticed that the game displays useful tips on the loading screen. That gave me the idea: what if I could show helpful tips or shortcuts for the tools I use every time I open a new terminal window?

# Let's Setup!

### Tips File
First, let's create a plain file and add tips that you need to show in it:
```sh
touch ~/.terminal-tips
```

I'm currently using it show only the `nvim` shortcuts, but I will add more in the future.
```sh
[nvim] i → Enter Insert Mode (start typing text into the file)
[nvim] :w → Save the current file (write changes to disk)
[nvim] :q → Quit nvim (only if no unsaved changes)
[nvim] :wq → Save and quit (writes changes, then exits)
[nvim] :q! → Quit without saving (discard all changes)
[nvim] 0 → Move cursor to the beginning of the current line
[nvim] $ → Move cursor to the end of the current line
[nvim] gg → Jump to the top of the file
[nvim] G → Jump to the bottom of the file
[nvim] u → Undo the last change
[nvim] Ctrl + r → Redo the change you just undid
[nvim] dd → Delete (cut) the current line
[nvim] yy → Copy (yank) the current line
[nvim] p → Paste copied/cut content **after** the cursor
[nvim] P → Paste copied/cut content **before** the cursor
[nvim] v → Start visual mode (highlight text for copying/deleting)
[nvim] V → Start visual line mode (select whole lines)
[nvim] :Ex → Open file explorer (browse files using netrw)
[nvim] /text → Search for "text" in the file
[nvim] :%s/old/new/g → Replace all "old" with "new" in the entire file
```

### Configure `~/.bashrc`

Add these lines in the bottom of the file:
```sh
# display tips randomly everytime it starts a new session
if [ -f "$HOME/.terminal-tips" ]; then
    TIP=$(shuf -n 1 "$HOME/.terminal-tips")

    # detect and colorize editor tag
    if [[ "$TIP" == *"[nvim]"* ]]; then
        TIP_WITH_COLOR=$(echo "$TIP" | sed 's/\[nvim\]/\\033[0;32m[nvim]\\033[0m/')
    elif [[ "$TIP" == *"[emacs]"* ]]; then
        TIP_WITH_COLOR=$(echo "$TIP" | sed 's/\[emacs\]/\\033[0;35m[emacs]\\033[0m/')
    elif [[ "$TIP" == *"[nano]"* ]]; then
        TIP_WITH_COLOR=$(echo "$TIP" | sed 's/\[nano\]/\\033[0;33m[nano]\\033[0m/')
    else
        TIP_WITH_COLOR="$TIP"
    fi

    echo -e  "\033[1;34m💡 Tip of the Day:\033[0m $(echo -e "$TIP_WITH_COLOR")"
fi
```
1. It looks for the file named `.terminal-tips`.
2. Then it randomly chooses a line using `shuf -n 1`.
3. We're using `if-else` conditions to display the text color of the tool name with different color to distinguish between the tools or the editors.

> Note: We need to make sure the `.terminal-tips` follows the same pattern all over the file, else it will be broken. It may show you the tips, but it can't display the tool name with different colors.

## Summary

Well, it not may be an instant fix for this problem but you will eventually see the benefits of this simple trick. Your forcing your brain to see the tips unconciously, everytime you open a new terminal window. I would say, that this method is effective for me and I started seeing the benefits over time. I still keep forgetting the shortcuts, but just the ones that are rarely used.
