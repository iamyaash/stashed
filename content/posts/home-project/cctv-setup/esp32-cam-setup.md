---
date: '2025-09-12T18:58:04+05:30'
draft: true
title: 'DIY: Home CCTV Setup Using ESP32-CAM'
summary: "If you want something, build it yourself, and that's exactly what I did. I crafted a CCTV camera with ~30 minutes battery backup, and the total cost was around ₹988 (about $11)."
tags:
- DIY
- CCTV
params:
    TocOpen: true
    ShowBreadCrumbs: true
cover:
    image: "posts/home-project/cctv-setup/esp32-cam-setup.png"
    alt: "ESP32-CAM board setup"
---
# Why this Setup?
For starters, I live in a village with **frequent power outages** that usually last for a couple of minutes before power is restored. It has gotten better compared to earlier days, but sometimes these outages can last up to ~30 minutes. So, I decided to **build something with at least ~30 minutes of power backup**.

A **wired camera setup looks bad, is slow, and is expensive**. If I go with a cheaper and longer (20-meter) cable, it cannot transmit the camera feed properly due to the lack of a repeater, and at best, the cable can only deliver a weak power supply ranging from 2–4.5V. Using a **longer cable pushes the cost beyond the budget** for a single camera setup. In this case, using a **wireless (IP) camera seemed like a good choice**.

Each camera setup should fit within the ₹1000 (~$10) budget. Additionally, since I already have some extra hardware, the entire budget can be allocated to the cameras itself.

## Components
| Product                                                         | Store                                                                                                                 | Price |
|-----------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------|------:|
| _ESP32-CAM WiFi Module Bluetooth with OV2640 Camera Module 2MP_ | [robocraze](https://robocraze.com/products/esp32-camera-module?_pos=1&_psq=ESP32-CAM+module+with+OV2640&_ss=e&_v=1.0) |  ₹636 |
| _TP4056 Adjustable 1A Li-ion lithium Battery Charging Module_   | [robu](https://robu.in/product/tp4056-1a-li-ion-lithium-battery-charging-module-with-current-protection-type-c/)      |   ₹12 |
| _MT3608 2A Max DC-DC Step Up Power Module Booster Power Module_ | [robu](https://robu.in/product/tp4056-1a-li-ion-lithium-battery-charging-module-with-current-protection-type-c/)      |   ₹35 |
| _1N5822 1W Schottky Diode_                                      | [robu](https://robu.in/product/tp4056-1a-li-ion-lithium-battery-charging-module-with-current-protection-type-c/)      |    ₹6 |
| _Dummy Fake Bullet Camera with Flashing Red Led_                | [amazon](https://www.amazon.in/dp/B0DSG864GQ?ref=ppx_yo2ov_dt_b_fed_asin_title)                                       |  ₹299 |