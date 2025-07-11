+++
title = ' Setting Up Raspberry Pi 5'
date = 2025-03-17T19:22:47+05:30
draft = false
tags = [ "docs", "setup", "raspberry" ]

[cover]
image = "posts/pi5/pi5-box.png"
alt = "pi5-box"
+++

I got my hands on the **Raspberry Pi 5** few weeks ago, but I wasn't able to use it for any purpose (more like I'm out of ideas). I mostly wanted to test the **`pihole`** in my network, but using **Pi 5** just for that would be an overkill!

> So I got a **Raspberry Pi Zero 2 W** and installed **`Pihole`** natively. [I wrote a post about it, feel free check-out](https://iamyaash.github.io/stashed/posts/pihole-pizero-2w/). Also, when I ordered for the **Raspberry Pi Zero 2 W**, I forgot to order for a microSD, so I used the one in the **Pi 5** which is a **64GB** microSD card (_I know it's an overkill!_). The entire **Pi Zero** is running just the **`Pihole`** with a 64GB microSD.

> - _Keep it mind that we're using the **Pi 5** as an headless machine!_
> - _I'm using the Fedora Linux for setting up **Raspberry Pi 5**_.

# Let's Install `Raspberry Pi OS`

1. Insert the microSD into a card reader and plug it into the computer.
    - Choose the device that you want to install as:
        - `Raspberry Pi 5 - Raspberry Pi 5, 500 and Computing Module 5`
    - Choose the Operating System as Raspberry Pi OS (other):
        - `Raspberry Pi OS Lite (64-bit) - A port of Debian Bookworm with no desktop environment`
    - Choose the Storage as the one that you plugged. (and click "Next"...)

2. It would show a prompt, make sure to edit them by clicking the "Edit Settings" button:
    - Set a custom **`hostname`**, **`username`** and **`password`** (_don't forget!_).
    - Configure the wireless network (_since you are going to be using a headless machine, make sure that it can automatically `ssh` into the network, else you won't be able to access it unless you connect a *keyboard & display*_).
    - Change the timezone and keyboard layout (_optional!_)
    - Head into **SERVICES** > Click "**Enable SSH - Use Password Authentication**"
    - Feel free to check other settings (if you completed the above mentioned steps, then you are done!)
3. Finally, click install it would download and install the OS in ~30 minutes.

# Let's `ssh` into the Pi 5

1. Login to your router and copy the IP address allocated the hostname `pi-master`.
2. Then, `ssh` into the **Pi 5**:
```sh
ssh username@192.168.1.x
```
> Replace `username` & `192.168.1.x` with the your credentials.

3. Update the packages
```sh
sudo apt update && sudo apt upgrade
```

By now, you would have a stable OS running in the Pi without any problems!