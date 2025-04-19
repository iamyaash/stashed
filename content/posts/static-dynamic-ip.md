+++
date = '2025-03-09T21:37:51+05:30'
draft = false
title = ' Definition of Static IP Address & Dynamic IP Address'
tags = [ "docs" ]

+++

# What is a static IP address?
A **static IP address is a fixed Internet Protocol (IP)** address assigned to a device, which does not change over time. It serves as a unique identifier within a network, enabling it connect & communicate effectively.

It is recommended to assign a static IP address to a device, which is always stationary & connected to the same network. Assigning static IP address on a frequently moving device will result in conflicts and blocks from either connecting to a router or accessing internet.

**Example**:
1. You set up a static IP address on your laptop connected to your home Wi-Fi network (`192.168.1.1` as the router's gateway).
```sh
interface wlan0
static ip_address=192.168.1.100/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8 8.8.4.4
```
This static IP address is configured to your router in home, you have a unique IP address and other devices can easily be connect/communicate with your device using this IP address, since it's never going to be changed.

2. You visit a coffee shop and connect to their Wi-Fi, where the router's gateway is `192.168.0.1`.
  - The static configuration on your laptop (`192.168.1.x`) does not match the coffee shop's network range (`192.168.0.x`), causing an IP conflict or failure to connect.
  - **Why is this happening ?**
    - Your laptop tries to use `192.168.1.100` and expects `192.168.1.1` as the gateway.
    - The coffee shop's router operates on a different subnet (`192.168.0.x`) and cannot route traffic correctly.

# What is a Dynamic IP Address?
A **dynamic IP address is a temporary address** allocated to the device you connect to a network. The temporary address is assigned to a device by DHCP server (Device Host Configuration Protocol) when it connects to a network. Unlike static IP addresses, which remain constant, dynamic IP's can change over time or when the device reconnects to the network.

It is to use recommended dynamic IP address when a device is frequently moving a lot and connects to different networks. Since the IP allocation is done by the network's router DHCP, the IP address will change according to the router default gateway.

> **Note**: Dynamic IP address is enabled by default in all other operating systems, it's only the static IP address allocation done manually.

---

# How to Assign a Static IP Address:
I looked at many "HowTo" videos, but I always ends up facing errors and we're going to be manually assigning them with minimal \
automated steps.

1. **Gather Network Information**:
```sh
hostname -I
ip r | grep default
route | grep '^default' | grep -o '[^ ]*$'
```
Output from my device:
```sh
iamyaash@pi-server:~ $ ip r | grep default
default via 192.168.31.1 dev wlan0 src 192.168.31.35 metric 3003 
iamyaash@pi-server:~ $ route | grep '^default' | grep -o '[^ ]*$'
wlan0
```
Copy the gateway address(`192.168.31.1`) & interface name (`wlan0`).

2. **Edit the DHCP Configuration File**:
Install `dhcpcd` unless there isn't any file named `/etc/dhcpcd.conf`:
```sh
sudo dnf install dhcpcd
```

```sh
sudo nano /etc/dhcpcd.conf
```
Add the following lines into the configuration file:

```sh
interface wlan0
static ip_address=<STATIC_IP>/24
static routers=192.168.31.1
static domain_name_servers=8.8.8.8 8.8.4.4 
```
Use any `<STATIC_IP>/24` to your liking. (Such as `192.168.31.253`)
Save and exit.

3. **Reboot the device to reflect the changes all over the system**:
```sh
sudo reboot
```
After the restart, ensure that the static IP is assigned in `hostname` & the changes are reflected to `/etc/resolv.conf`.
```sh
hostname -I
```
```sh
cat /etc/resolv.conf
```

### Follow these steps only if the Static IP Address is not Reflected anywhere
1. **Stop & Disable the `systemd-resolved.service`**:
```sh
sudo systemctl stop systemd-resolved.service
sudo systemctl disable systemd-resolved.service
```
This will stop & disable the `systemd-resolved.service`, which no longer manages DNS resolution, allowing you to fully control the `/etc/resolv.conf` file.

2. **Remove the Symlink for `/etc/resolv.conf`**:
```sh
sudo rm /etc/resolv.conf
```
> Typically, the `/etc/resolv.conf` is linked to systemd-resolved`.

3. **Create a new `/etc/resolv.conf`** & Manually Add the DNS Addresses:
```sh
sudo nano /etc/resolv.conf
```
```sh
nameserver 208.67.222.222
nameserver 208.67.220.220
nameserver 8.8.8.8
```
Save and exit.

4. Restart the `dhcpcd` service:
```sh
sudo systemctl restart dhcpcd
```

### Delete the existing IP, in case the older IPs are retained:
```sh
sudo ip addr del 192.168.31.102 dev wlan0
```
- `192.168.31.102` - The IP that needs to be removed.
- `dev wlan0` - The interface name of the `IP` address.

# If nothing works out, let's change the static IP using `nmcli`:

1. List the available networks
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

2. Modify the `Wired connection 1` network:
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

4. If you don't need IPv6, you can disable it for this connection (Optional):
```sh
sudo nmcli connection modify preconfigured ipv6.method ignore
```