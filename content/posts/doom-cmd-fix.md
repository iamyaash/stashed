+++
date = '2025-01-31T18:44:50+05:30'
draft = false
title = 'Doc : `doom` Installation & Unrecognized Command Fix'
tags = [ "docs", "troubleshoot", "script" ]

+++

# Install Doom Emacs 
Head over to this [repository](https://github.com/doomemacs/doomemacs?tab=readme-ov-file#install) and execute the scripts.
```sh
git clone --depth 1 https://github.com/doomemacs/doomemacs ~/.config/emacs
~/.config/emacs/bin/doom install
```

## `doom` command not recognized

Whenever I install `doom emacs`, I always face this problem and now we're going to put an end to this error.(!panic)

The error would look something like mentioned below:

```sh
Error: file-missing ("Cannot open load file" "No such file or directory" "/usr/early-init.el")
  mapbacktrace(#f(compiled-function (evald func args flags) #<bytecode -0x1a9589d6c510f34>))
  debug-early-backtrace()
  debug-early(error (file-missing "Cannot open load file" "No such file or directory" "/usr/early-init.el"))
  load("/usr/early-init.el" nil nomessage nosuffix)
  (and (load init-file nil 'nomessage 'nosuffix) (featurep 'doom))
  (or (and (load init-file nil 'nomessage 'nosuffix) (featurep 'doom)) (user-error "Failed to load Doom from %s" init-file))
  (let* ((bin-dir (file-name-directory (file-truename load-file-name))) (init-file (expand-file-name "../early-init.el" bin-dir))) (or (and (load init-file nil 'nomessage 'nosuffix) (featurep 'doom)) (user-error "Failed to load Doom from %s" init-file)))
  (condition-case e (let* ((bin-dir (file-name-directory (file-truename load-file-name))) (init-file (expand-file-name "../early-init.el" bin-dir))) (or (and (load init-file nil 'nomessage 'nosuffix) (featurep 'doom)) (user-error "Failed to load Doom from %s" init-file))) (user-error (message "Error: %s" (car (cdr e))) (kill-emacs 2)))
  load-with-code-conversion("/usr/bin/doom" "/usr/bin/doom" nil t)
  command-line-1(("--load" "/usr/bin/doom" "--"))
  command-line()
  normal-top-level()
Cannot open load file: No such file or directory, /usr/early-init.el
```

**Let's fix it:**

1. First, we have verify that we have the required directory present in the respective location.
  - `~/.config/emacs`
  - `~/.config/doom`
  - Basically, these directories are enough to get started!
2. However, make sure there's no directory named `emacs.d` in home directory of the user (`~/.emacs.d`).
  - If there's a directory present in this location(`~/.emacs.d`), then it will become error.

Make sure to verify **1 & 2** .
> The error mainly is caused because of the **`2`**, so make sure that directory is not present.

*Alternative*:
If that directory is needed, then we can directly point `doom` there instead of `~/.config/doom`

**Let's get fix it simple command!**:
1. Open `~/.bashrc`
```sh
nano ~/.bashrc
```
2. Add this line at the bottom:
```bash
export PATH="$HOME/.config/emacs/bin:$PATH"
```
3. Update the changes:
```sh
source ~/.bashrc
```
