---
date: '2025-12-06T17:41:43+05:30'
draft: false
title: "GPG: Fix 'database_open waiting for lock'"
summary: "Shows 'database_open waiting for lock', which is blocking me from signing commits. GPG creates a temporaray lock file in ~/.gnupg/*.lock* (like pubring.db.lock) which prevent the database access."
tags: [ "GPG" ]
params:
    author: "Yashwanth Rathakrishnan"
    ShowToc: true
    TocOpen: true
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowBreadCrumbs: true
---

# Overview
Git commit signing fails because GPG cannot access its GPG key database due to a lock held by process ID `<random-process-id-number>` (in my case it's `ID 101853`). It repeatedly shows "`database_open waiting for lock`" messages and eventually gets timeout on GPG key search and signing. In simple terms, this error simply blocks me from signing commits.

## Why it happens?
GPG created a temporary lock files in `~/.gnupu` and the extension of the files ends with `*.lock*`. I don't know the exact reason, but if you are facing the same problem as me then it's definitely because of this lock files and a background process is running behind which is keeping the files intact. As far as I have noticed, it seems like GPG somehow crashed and the process leaves the stale locks behind. Which is preventing us from signing commit using the gpg keys.

## How to diagnose?
I can't find the process ID 101853 to terminate, but there's process running behind and I can't find any GPG services running behind as well:
```sh
iamyaash@fedora:~/Projects/codeberg/forgejo-official$ ps -p 101853
   PID TTY          TIME CMD
iamyaash@fedora:~/Projects/codeberg/forgejo-official$ psgrep gpg
iamyaash   33108  0.0  0.0 231256  2328 pts/3    S+   17:24   0:00  |       \_ grep --color=auto gpg
```
So I tried looking into the files ends with `*.lock` in `~/.gnupg` and I found a file named `pubring.db.lock` which is the culprit here:
```sh
iamyaash@fedora:~/Projects/codeberg/forgejo-official$ find ~/.gnupg/ -name '*.lock'
/home/iamyaash/.gnupg/public-keys.d/pubring.db.lock
```

I found out that deleting the file didn't help either and the issue still persist.
```sh
iamyaash@fedora:~/Projects/codeberg/forgejo-official$ find ~/.gnupg/ -name '*.lock' -delete
/home/iamyaash/.gnupg/public-keys.d/pubring.db.lock
```

```sh
iamyaash@fedora:~/Projects/codeberg/forgejo-official$ echo "test" | gpg --clearsign
gpg: Note: database_open 134217901 waiting for lock (held by 101853) ...
gpg: Note: database_open 134217901 waiting for lock (held by 101853) ...
gpg: Note: database_open 134217901 waiting for lock (held by 101853) ...
gpg: Note: database_open 134217901 waiting for lock (held by 101853) ...
gpg: Note: database_open 134217901 waiting for lock (held by 101853) ...
gpg: keydb_search failed: Connection timed out
gpg: no default secret key: Connection timed out
gpg: [stdin]: clear-sign failed: Connection timed out
```


## How to fix?
The primary reason for this issue is because, **locks from crashed session kept the keys locked even after `PID 101853` killed/terminated**. 

The typical **solution seemed to be the full reset of '`gpgconf`'**.
```sh
gpgconf --kill gpg-agent
gpgconf --kill keyboxd
pkill -f gpg-agent
pkill -f keyboxd

gpgconf --reload gpg-agent
gpgconf --reload scdaemon
```

This simple reset and reload seems to fix the issue:
```sh
iamyaash@laptop:~/Projects/gh/iamyaash/stashed$ echo "test" | gpg --clearsign
-----BEGIN PGP SIGNED MESSAGE-----
Hash: SHA512

test
-----BEGIN PGP SIGNATURE-----
    ...
    ...
    ...
    ..
-----END PGP SIGNATURE-----
```
