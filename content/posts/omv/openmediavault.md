+++
title = ' Open Media Vault (OMV) in R-Pi'
date = 2025-04-14T11:28:49+05:30
draft = false
summary = 'How to install OpenMediaVault & troubleshoot errors after during installation  or setup' 
tags = [ "docs", "setup", "server", "script"]


[cover]
image = "posts/omv/omv.png"
alt = "OpenMediaVault-screenshot"
+++

Note: This is a per

# Prerequisites
1. Raspberry Pi Lite 64-bit Operating System installed on a **Raspberry Pi**.
2. Wired R-Pi connection to network and connected with display and keyboard as well. (_We're not configuring it wirelessly_)

## Let's Install OMV After Setting Up The Operating System

Make sure to update & upgrade the operating system, before installing **`openmediavault`**. ([_*Installation Doc*_](https://wiki.omv-extras.org/doku.php?id=omv7:raspberry_pi_install))
```sh
sudo apt-get update && sudo apt-get upgrade -y
```

1. Preinstall OpenMediaVault 
```sh
wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/preinstall | sudo bash 
```
2. Reboot the device after finishing the pre-installation part
```sh
sudo reboot
```
3. Let's do the Main Installation!
```sh
wget -O - https://github.com/OpenMediaVault-Plugin-Developers/installScript/raw/master/install | sudo bash 
```

> **Note**: It take some time to run omv, wait for 2-4 minutes and open the OMV.


## First Login
Use the device's IP address to login to OMV.

**Username**:
```sh
admin
```
**Password**:
```sh
openmediavault
```

Make sure to change password the after logging in, 
```
User Settings (Top Right Icon) > Change Password
```


# Setup the Storage Sharing
_Note: I'm only using a Flashdrive as the storage medium_

### Adding Storage Device

1. Storage > File Systems > Mount. (_Click to Mount an Existing File System_)
2. Select the File System shown in the drop-down and click "Save".

### Allowing Share Access to the Storage Device
1. Storage > Shared Folders > Create.
2. Enter the directory "Name" of your choice, select the file system which was Mounted before and click "Save".
3. Click upon the shared storage and click "Permissions" on top of navbar to change the permissions of access.
4. Select whichever the permission work best for the user and click "Save".


# Setup Docker
1. System > Plugins > Search "docker"
2. Install the plugins that are displayed in the search results.
3. Services > Compose > Settings > Select the "Shared Folder" for **Compose Files** & **Backup** and click "Save".
4. Click "Reinstall Docker" in bottom section.
5. Let's create a user for accessing `docker`. Users > Users > Create User > username & password > select `groups`
- docker, 
- openmediavault-admin, 
- openmediavault-config, 
- sambashare, 
- users

Check whether the user changes are reflected in the device, by `SSH`'ing into the OS:
```sh
id admin
id <username-entered-for-docker-access>
# This will return something like...
uid=1003(docker-user) gid=100(users) groups=100(users),991(sambashare),990(openmediavault-config),988(openmediavault-admin),985(docker)

```

### Deploy an application using `compose`
1. Services > Compose > Files > Add
2. Copy the `docker-compose` under the "File" textarea
3. Make sure to match of your device's configurations:
- PUID 
- PGID
> Gathered from `id docker-username` outpur in terminal.
- TZ
- PORTS
- Volume
> Storage > Shared Folders > Copy Absolute Path

**Original**:
```sh
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes:
      - /path/to/heimdall/config:/config
    ports:
      - 80:80
      - 443:443
    restart: unless-stopped
```

**Modified for OMV**:
```sh
services:
  heimdall:
    image: lscr.io/linuxserver/heimdall:latest
    container_name: heimdall
    environment:
      - PUID=1003
      - PGID=100
      - TZ=Asia/Kolkata
    volumes:
      - /path/to/heimdall/config:/config
    ports:
      - 8080:80 # preferable to change
      - 443:443
    restart: unless-stopped
```
### Start the application:
1. Services > Compose > Files > Click "Up"
2. This will start the application and also display the ports to visit the running application.
