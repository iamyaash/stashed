+++
date = '2025-03-13T21:43:22+05:30'
draft = false
title = ' Installing Pihole in Raspberry Pi Zero 2W'
tags = [ "docs", "setup", "network" ]

+++

I just got my hands on "Raspberry Pi Zero 2W" today, let's setup the Pi!

# Setup the `Pi`
> Make sure to use `pi-imager` for writing the OS into the microSD. Also use a router as well for **easy-setup**.

1. Choose "**Raspberry Pi OS 32-bit with no desktop environment**" for the OS.
2. Make sure to edit the **username** & **password**, **SSID** configurations, **enable SSH using authentication**, additonally **note-down the hostname** for `ssh` connection.
3. Insert the microSD into Pi Zero after the OS installation.
4. Plug in the microUSB into Pi and wait for few seconds to boot up.
5. Head inside the router web-page and look into the connected clients.
6. Identify the hostname (default:"raspberrypi") and note-down the IP address.
7. Let's `ssh` into the Pi!
```sh
ssh username@192.168.31.xxx
```
Now you are inside the Pi, please update & upgrade the packages in it for further installations.
```sh
sudo apt update && sudo apt upgrade
```

---

# Let's Assign a Static IP Address
1. **List the available networks**
```sh
sudo nmcli connection show
```
> This will show the available networks connected to your device.
**Output:**
```sh
NAME                UUID                                  TYPE      DEVICE  
Wired connection 1  82aadf2b-xxxx-xxxx-a3ec-xxxxxxxxxxxx  ethernet  enp2s0  
lo                  e6b49b80-xxxx-xxxx-b8d7-xxxxxxxxxxxx  loopback  lo      
docker0             bfcf407f-xxxx-xxxx-bbfe-xxxxxxxxxxxx  bridge    docker0 
```

2. **Modify the `Wired connection 1` network**:
```sh
sudo nmcli connection modify "Wired connection 1" ipv4.addresses 192.168.31.xx/24 ipv4.gateway 192.168.31.1  ipv4.dns "8.8.8.8 8.8.4.4" ipv4.method manual
```

3. **Restart the Connection**
After modifying the connection, restart it to apply the changes:
```sh
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"`
```

3. **Verify the New IP Address**
Check if the static IP has been assigned successfully:
```bash
ip addr show wlan0 | grep inet
```

4. **_If you don't need IPv6, you can disable it for this connection (Optional)_**:
```sh
sudo nmcli connection modify preconfigured ipv6.method ignore
```

# Let's Install Pi-Hole and set things up!

1. *Install Pihole*
```sh
curl -sSL https://install.pi-hole.net | bash
```
2. _Just keep clicking it_! (stick with defaults)
3. _Make sure to note the password that will be generated at the end of the installation_.
4. _Login to the web-page after the installation_. (https://192.168.xxx.xxx/admin/)

## Let the Pihole handle DHCP
1. **Disable the DHCP handled by your Router**.
2. *Enable DHCP in pihole* (Setting > DHCP )
  - Enter the starting range - `192.168.xxx.100`
  - Enter the ending range - `192.168.xxx.200`
  - Enter the default gateway - `192.168.xx.x`
  - Enter the netmask - `255.255.255.0`

> **Note:** We're letting `pihole` handle everything, such network level advertisement & tracker blocking and DHCP handling.

After this everything would setup properly and you're good to go!