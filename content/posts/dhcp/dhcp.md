+++
title = " Definition of DHCP & Practical Explanations"
date = "2025-03-15T05:51:27.000Z"
draft = false
tags = [ "docs", "explanation", "pizero" ]

[cover]
image = "posts/dhcp/pihole-settings-dhcp.png"
alt = "pihole-settings-dhcp-screenshot"
+++

# What is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** is a network management protol that automates the assignment of IP address to the devices that connect to a network and it also handles other network configuration parameters such as **subnet mask, default gateway and DNS settings**.

### Why do we need IP address anyway?
Let's take an example, You visit a coffee shop and connect to it's Wi-Fi network. In doing so, you are essentially connecting to the shop's router, which provides the internet connection. The router itself is connected to an internet service provider(_**ISP**_) via a modem or an antenna(_in case of wireless broadband_).

When you attempt to connect, your device needs an IP address to communicate over the network. This is where DHCP comes in. The router's DHCP server dynamically assigns an IP address from its available pool to your device and all the other device that attempts to establish a connection.

#### How does DHCP Assign an IP Address?
For the DHCP server to allocate an IP address, the router must have a default gateway address, which servers as the network's main access point.

A commonly used default gateway IP address is **`192.168.1.1`**(_though this may vary by router and configuration_). The associated subnet mask is typically **`255.255.255.0`**.

- `255.255.255.0` - These number indicate the fixed portion of the network, identifying the network itself (`192.168.1`).
- `0 in (255.255.255)` - This means that devices on the network can be assigned IP addresses within the range `192.168.1.1` to `192.168.1.254`.
  - However, we only have `253` IP address available in the pool to be used.
  - Because, `.0`, `.1` and `.255` are reserved
    - The `.0` is known as the **Network Address** and it holds some superiority over the other IP addresses.
    - The `.255` is know as the **Broadcast Address** and it's used to broadcasting a message.
    - The `.1` is as you know, it hold the **Default Gateway Address** of the router itself.

### How does DHCP Works?

The DHCP process consists of four key steps, (**aka DORA**):

1. **Discovery**: The client broadcasts a request to find a DHCP server.
2. **Offer** The DHCP server responds with an available IP address and configuration details.
3. **Request**: The client selects the offered IP and requests to lease it. (_since it's dynamic IP, it'll be temporary or expire soon_)
4. **Acknowledgement**: The server confirms the lease, and the client starts using the assigned IP address.


## Replacing the router's DHCP server with Pihole's DHCP server:
I've recently got my hands on **Raspberry Pi Zero 2W** and I installed Pihole in it. At first, I was using the router's DHCP server, but it's outdated(_functional though_) and as an **Admin** I have no control over it(_UI is outdated as well_). So I disable the DHCP server in the router, and enabled the DHCP server in pihole.

- Now I have control over the pool that I want to be allocated for the devices that are attemping to connecting.
    - i.e, I have set the range of IP addresses to hand out from(`Start`) `.100` to (`End`) .200
- Shows the details of the connected client with a neat user-interface.
- Holds control of DHCP server for almost anything.