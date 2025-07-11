+++
title = ' Restic, On How to Backup Efficiently'
date = 2025-04-18T20:48:28+05:30
draft = false
tags = [ "docs", "setup", "backup" ]

[cover]
image = "posts/backup/restic.png"
alt = "restic-screenshot"
+++

This is a really interesting backup application to use, it kinda reminds me of `git` the version controlling tool. I mean `restic` is not gonna "version control" but it uses the same technology in a way that enables us to backup things without duplication. I'm using `restic` to backup the files in a `sftp` server. So this post is gonna cover the `sftp` aspects.

However, I'm using a mobile phone as an `ftp` server, and I'm going to be using a weird directory name.

# Let's Initialize the Repository in the `sftp` server.
> **Note**: Make sure to establish a connection between the `sftp` server and your device.

Let's establish a connection your mobile FTP server by noting down the IP address & it's port address. Go to your "Network" folder in file explorer in your Operating System and enter the same address to establish a connection.

After making a successful connection, ssh into the FTP server directory or just "Open a Console" under the directory to get access into server without explicitly mentioning `ssh`. Use `pwd` to display the storage address and note it down, because it will be used for specifying the `init` location of backup.

```sh
restic init --repo "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server
```
If the backup is being performed in an actual `sftp ` server, then replace the `--repo <address>` with `sftp:user@host:<directory-location>`

# How to Backup & Display the Snapshots
All the backups will be stored in snapshots, hence everytime the backup is initiated, it compares the existing file to prevent duplication and only backups the modified or newly created files.
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" backup --verbose ~/Documents/ ~/Pictures
```

**To Display the Snapshots**
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" snapshots
```
This will display the available snapshots.
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" snapshots 
enter password for repository: 
repository 45b26599 opened (version 2, compression level auto)
ID        Time                 Host        Tags        Paths                     Size
--------------------------------------------------------------------------------------------
7b6bb481  2025-04-16 23:42:27  fedora                  /home/iamyaash/.ssh       48.436 KiB

c18b4633  2025-04-16 23:42:50  fedora                  /home/iamyaash/.gnupg     67.951 KiB

a925118b  2025-04-16 23:53:43  fedora                  /home/iamyaash/.task      704.182 KiB
                                                       /home/iamyaash/.taskrc

980cd67a  2025-04-17 00:06:29  fedora                  /home/iamyaash/Documents  204.987 MiB

85d5bb7c  2025-04-17 00:12:08  fedora                  /home/iamyaash/Pictures   127.424 MiB

3452ec11  2025-04-18 19:09:18  fedora                  /home/iamyaash/Downloads  198.870 MiB
--------------------------------------------------------------------------------------------
6 snapshots
```

# How to Restory the Backups
Restoring the backup or snapshots is really as easy as it sounds, just use the following the commands to restore the contents of the latest `snapshot`
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" restore a925118b --target /home/iamyaash
```
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" restore latest a925118b --target /home/iamyaash
```
> `restore latest` will restore the latest backup of the selected snapshot.

# Let's Cover the Other Features
1. **`--dry-run`**:

Will let you perform backup to see what would happend without actually making backups or modifying the repository.

```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" --dry-run --verbose | grep added
```

2. **`diff`**:
To compare two existing snapshots in the repository.
```sh
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" diff 980cd67a 85d5bb7c
```

3. **`mount`**:
This is an interesting method where we'll mount the snapshot directory instead of extracting the backup.
```sh
mkdir /mnt/restic #if the directory doesn't exist
restic -r "/run/user/1000/gvfs/ftp:host=192.168.31.177,port=2221/Server" mount /mnt/restic
```

