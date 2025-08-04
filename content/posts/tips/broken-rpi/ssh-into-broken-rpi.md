---
date: '2025-07-11T15:46:49+05:30'
draft: false
title: 'Regaining SSH Access To Headless Raspberry Pi After Network Changes'
summary: 'I had to reset my network router, which caused me to lose connection to my Raspberry Pi. In this guide, I’ll show you a simple way to restore access and reconnect to your Raspberry Pi without needing a monitor.'
tags:
  - tips
  - raspberry
  - headless
Params:
    ShowReadingTime: true
    ShowCodeCopyButtons: true
    ShowBreadCrumbs: true
cover:
    image: posts/tips/broken-rpi/lost-ssh-conn.png
    alt: Connection Lost
---

# Context
I'm using Pi-Hole in a Raspberry Pi Zero 2W, and I have not installed any thermal pad or cooling paste on it. It rarely gets turned off and would not work for few minutes. Only, if I disconnect the power cable, leave it there for minutes and plugging it again will work. I was postponing to diagnose the device and yesterday it finally happened and I started digging in. 

I was searching the internet for any way to get SSH connection into the device, but I found no solution that utilized headless connection. Because, I don't have the type of HDMI cable that support the **Raspberry Pi Zero 2w**. So, I had to find a way to make it connect to the new network without deleting the data inside it.