---
date: '2025-08-22T14:01:32+05:30'
draft: false
title: 'Efficient Way To Screen Mirroring'
summary: "Quick guide on how to mirror screen wired/wirelessly using scrcpy. Explains how to connect more than one device wireleslly and listed some of the useful flags to use."
tags:
- scrcpy
- utils
params:
    author: "Yashwanth Rathakrishnan"
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowToc: true
    TocOpen: true
    ShowBreadCrumbs: true
cover:
    image: posts/tips/screen-mirror/img/scrcpy.png
    alt: Connection Lost
---

# What is scrcpy?
`scrcpy` is a free, open-source tool that lets you control your Android device's screen on your computer using USB/WiFi.

**1. Install `scrcpy` and `adb`**:
- RPM-based distributions
```sh
sudo dnf install adb scrcpy
```
- Debian-based distributions
```sh
sudo apt install adb scrcpy
```
**2. Enable "Developer Options" and USB Debugging**:
- Tap `Build Number` 7 times to enable Developer Options.
- Go to `Setting > Developer Options > Enable USB Debugging`

**3. Connect and Start `scrcpy`**:
- Connect your phone to your PC using a USB cable.
- Type `scrcpy` and press enter. Your default available phone screen will appear on your PC. You can freely interact with it.

## Basic Usage

| Use Case                         | Command Examples         |
|----------------------------------|--------------------------|
| Mirror default screen            | `scrcpy`                 |
| Lower Resolution                 | `--max-size 720`         |
| Lower Frame Rate                 | `--max-fps 30`           |
| Show Touch (_temporary-overlay_) | `--show-touches`         |
| Prevent Sleep                    | `--stay-awake`           |
| Record Screen                    | `--record filename.mp4`  |
| Full Screen                      | `--fullscreen`           |
| Multiple Device Support          | `--serial SERIAL_NUMBER` |

## Wireless Connection Setup
1. Connect via USB  & Run `adb tcpip 8888`:
```sh
# check if the device is connected
adb devices
-------------------------
List of devices attached
QV7123123K      device
```

```sh
abd tcpip 8888
```
After executing the above command, your phone will breifly disconnects and reconnect to `adb`.

2. Disconnect the USB cable from your phone and use this command:
```sh
adb connect <IP_ADDRESS>:8888
```
> `<IP_ADDRESS>` is the IP address of your phone connected to the same wifi network.

```sh
adb devices
List of devices attached
192.168.xx.xx:8888     device
```

3. Start `scrcpy`:
```sh
scrcpy
```
It will automatically connects to the device wirelessly. You phone's screen should appear on your computer. You can interact wirelessly as if you were still connected via USB. 

4. Optional: Adjust Quality For Wireless Screen Sharing For Optimal Performance
```sh
scrcpy --bit-rate 2M --max-size 800
```
## Optional: Connect more than one device
Just use `adb connect` with the IP address of the other phone under the same port:
```sh
adb connect 192.168.31.101:8888
```
- In case you see errors like this:
    - "failed to connect to '192.168.31.101:8888': Connection refused"
    - Stop and Restart the Daemon:
    ```sh
    adb kill-server
    adb start-server
    ```
    - Start `tcpip` port again
    ```sh
    adb tcpip 8888
    ```
    - Repeat the same steps as above to connect phone via USB & Setup.
- When you have more than one device, you need to use the flag `--serial`/`-s`:
```sh
#if you just just the scrcpy command, it this show this message
ERROR: Multiple (2) ADB devices:
ERROR: Select a device via -s (--serial), -d (--select-usb) or -e (--select-tcpip)
ERROR: Server connection failed
```
```sh
# make sure to <IP_ADDRESS>:<PORT>
scrcpy --serial "192.168.xx.xx:8888"
```