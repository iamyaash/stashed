+++
title = 'Linux Directories'
date = 2025-03-30T19:41:36+05:30
draft = false
+++

# Let's move into the `/` (_root_) direcory:

```bash
iamyaash@fedora:/$ ll
total 20
dr-xr-xr-x.   1 root root    0 Jul 17  2024 afs
lrwxrwxrwx.   1 root root    7 Jul 17  2024 bin -> usr/bin
dr-xr-xr-x.   6 root root 4096 Mar 26 02:33 boot
drwxr-xr-x.  21 root root 4400 Mar 30 14:23 dev
drwxr-xr-x.   1 root root 4870 Mar 28 21:04 etc
drwxr-xr-x.   1 root root   16 Feb 23 23:19 home
lrwxrwxrwx.   1 root root    7 Jul 17  2024 lib -> usr/lib
lrwxrwxrwx.   1 root root    9 Jul 17  2024 lib64 -> usr/lib64
drwx------.   1 root root    0 Oct 24 20:17 lost+found
drwxr-xr-x.   1 root root    0 Jul 17  2024 media
drwxr-xr-x.   1 root root    0 Jul 17  2024 mnt
drwxr-xr-x.   1 root root   48 Feb 23 18:34 opt
dr-xr-xr-x. 576 root root    0 Mar 30  2025 proc
dr-xr-x---.   1 root root  168 Mar 19 13:11 root
drwxr-xr-x.  58 root root 1500 Mar 30 17:30 run
lrwxrwxrwx.   1 root root    8 Jul 17  2024 sbin -> usr/sbin
drwxr-xr-x.   1 root root    0 Jul 17  2024 srv
dr-xr-xr-x.  13 root root    0 Mar 30 14:22 sys
drwxrwxrwt.  28 root root  720 Mar 30 19:43 tmp
drwxr-xr-x.   1 root root  168 Oct 24 20:19 usr
drwxr-xr-x.   1 root root  200 Oct 24 20:27 var
```

**1. `/bin`** 
  - This directory contains the essential binaries (executables) required for the operating  system to function properly. (_e.g: `ls`, `pwd`, `cat`, `ln`, `vim`, `nano`_).
binaries can be accessed anywhere in the device.
  - In simple terms, it holds crucial applications that can be accessed from anywhere on the system. (_no `user` is blocked from using this binaries_)

**2. `/sbin`**
  - This contains system binaries, which means that it can only be used in either the `sudo` mode or logged in as a `root` user. (_e.g: `mount`, `deluser`, `adduser`_)

**3. `/lib`**
  - Many of the above mentioned directories, may share the command libraries. Which are stored inside in the `/lib` directory.
```sh
iamyaash@fedora:/$ ll lib
lrwxrwxrwx. 1 root root 7 Jul 17  2024 lib -> usr/lib
```
> (_In modern Linux distributions, /lib is often a symbolic link to /usr/lib._)

**4. `/usr`**
- **`/usr/bin`** - This directory contains non-essential binaries, meaning the programs stored here are not required for the system to boot or run in single-user mode. These binaries are typically used by regular users rather than the system itself.
  - The `/usr/bin` directory holds most of the user-space applications and utilities that are not essential for basic system operation. These include text editors (`nano`, `vim`), compilers (`gcc`), package management tools (`dnf` or `apt`), and various system utilities.
  - Unlike `/bin`, which contains critical system binaries needed for booting and recovery, `/usr/bin` is meant for general-purpose applications. In many modern distributions, the distinction between `/bin` and `/usr/bin` is blurred due to the `/usr` merge, where `/bin` is often a symbolic link to `/usr/bin`.

- **`/usr/loca/bin`** - This directory contains binaries that are compiled and installed locally by the user, ensuring they do not conflict with software installed by the system package manager.
  - In simple terms, it provides a safer place to store locally compiled binaries, preventing conflicts with executables installed via a package manager.
  - **Example**: _Suppose you download and compile a newer version of `htop` from source instead of installing it via `dnf` or `apt`. If you install it to `/usr/local/bin`, it will not overwrite the system-managed version in `/usr/bin`_.
  - **Note**: `which` command shows the current binary it uses for the command.
  ```bash
  $ which curl
  /usr/bin/curl
  ```

**5. `$PATH`**
  - The `$PATH` variable defines directories where the system looks for executable binaries.
  - This allows us to run programs from any directory without specifying their full path.

**6. `/etc`**
  - `etc` stands for **E**ditable **T**ext **C**onfig.
  - Most of the filenames ends with `.conf` and typically consists of text based files that can be modified using any text editor with `sudo`.
  - These files primarily control system and software behavior. (_e.g., `/etc/resolv.conf` stores network-related configurations._)

**7. `/home`**
  - This directory contains user-specific data, including personal files, configuration settings, and user-installed software.
  - Each user has their own subdirectory (`/home/username`), which they own and control.
  - To access another user's home directory, you must either log in as that user or have appropriate read permissions. 

**8. `/boot`** - This is literally the kernal itself. (_It contains the data for booting the system_)

**9. `/dev`** - We can use this directory to interface with hardware or drivers as if there were regular files.

**10. `/opt`** - Contains optional or add-on sofware that we rarely interact with.

**11. `/var`** - Contains variabl files that will change as the operating system is being used. (eg. `system_logs`)

**12. `/tmp`** - Consist of temporary files that won't persisted between reboots.

**13. `/proc`** - It contains special files that show real-time information about the system, processes, and hardware. Unlike normal files, these are virtual files that update dynamically.

  - proc/cpuinfo → Shows details about your CPU (like speed, cores, model).
  - /proc/meminfo → Shows memory (RAM) usage.
  - /proc/uptime → Tells how long your system has been running.
  - /proc/1234/ → A folder for process ID 1234, containing details about that