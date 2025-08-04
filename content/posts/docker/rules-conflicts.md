---
date: '2025-08-04T12:02:02+05:30'
draft: false
title: 'Rules Conflicts Between Firewalld & Iptables in Docker'
summary: 'An overview of conflicts between iptables and firewalld in Docker containers.'
tags:
  - tips
  - docker
Params:
    ShowReadingTime: true
---
# The Problem
I am primarily using **`firewalld`** for managing the systems with fine-grained access controls. But I recently installed `docker` on my Raspberry Pi Zero 2W, and started a [Forgejo](https://forgejo.org/) container in port `3000`. Additionally, I have setup [Pihole](https://pi-hole.net/) on Rasberry Pi. Which used port 53, 80, 443 & 67.

```sh
iamyaash@pi-network:~ $ sudo ss -tulnp | grep pihole
udp   UNCONN 0      0       0.0.0.0:53         0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=22))
udp   UNCONN 0      0       0.0.0.0:67         0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=20))
udp   UNCONN 0      0       0.0.0.0:123        0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=33))
udp   UNCONN 0      0             *:53               *:*    users:(("pihole-FTL",pid=642,fd=24))
udp   UNCONN 0      0             *:123              *:*    users:(("pihole-FTL",pid=642,fd=43))
tcp   LISTEN 0      200     0.0.0.0:80         0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=37))
tcp   LISTEN 0      32      0.0.0.0:53         0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=23))
tcp   LISTEN 0      200     0.0.0.0:443        0.0.0.0:*    users:(("pihole-FTL",pid=642,fd=38))
tcp   LISTEN 0      200        [::]:80            [::]:*    users:(("pihole-FTL",pid=642,fd=39))
tcp   LISTEN 0      32         [::]:53            [::]:*    users:(("pihole-FTL",pid=642,fd=25))
tcp   LISTEN 0      200        [::]:443           [::]:*    users:(("pihole-FTL",pid=642,fd=40))
```

Here's the allowed ports in `firewalld`:
```sh
iamyaash@pi-network:~ $ sudo firewall-cmd --zone=piholezone --list-ports 
53/tcp 80/tcp 443/tcp 2424/tcp 53/udp 67/udp
```

I noticed that I can still access forgejo container in port `3000`. Which is weird and not making sense, because I have clearly mentioned that I allow access to only 53, 80, 443, 2424, & 67. How come I can access port `3000` without any restrictions.


Here you can see the port `3000` running:
```sh
iamyaash@pi-network:~ $ sudo ss -tulnp | grep 3000
tcp   LISTEN 0  4096    0.0.0.0:3000    0.0.0.0:*    users:(("docker-proxy",pid=1358,fd=7))
tcp   LISTEN 0  4096       [::]:3000       [::]:*    users:(("docker-proxy",pid=1366,fd=7))
```


### Let's recap, 
- Using `firewalld` primarily for managing port access
- Only allowed port `53, 80, 443, 67 & 2424`
- Running Forgejo container on port `3000`, which should not supposed be accessible but I can


## What really happened?

Even though I have set access to specific ports, it somehow allows access to other ports. It's because Docker creates it's own `iptables` rules for container network traffic, which effectively take precedence over `firewalld`'s zone-based rules for relevant Docker network interfaces.

This means, Docker's `iptables` rules allow access to port `3000` as configured, bypassing firewalld's zone restrictions on your interface.

You can check the actual iptables rules that Docker created, which allowed port `3000` regardless of firewalld zones:
```sh
sudo iptable -L -n -v | grep 3000
```
You can check nftables rules as well:
```sh
sudo nft list ruleset | grep 3000
```

### Output:
```sh
iamyaash@pi-network:~ $ sudo iptables -L -n -v | grep 3000
6   360 ACCEPT  6   --  !br-3ea8f4e2b7c7 br-3ea8f4e2b7c7    0.0.0.0/0 172.18.0.2   tcp dpt:3000
```
```sh
iamyaash@pi-network:~ $ sudo nft list ruleset | grep 3000
# Warning: table ip nat is managed by iptables-nft, do not touch!
iifname != "br-3ea8f4e2b7c7" tcp dport 3000 counter packets 6 bytes 360 dnat to 172.18.0.2:3000
# Warning: table ip filter is managed by iptables-nft, do not touch!
iifname != "br-3ea8f4e2b7c7" oifname "br-3ea8f4e2b7c7" ip daddr 172.18.0.2 tcp dport 3000 counter packets 6 bytes 360 accept
# Warning: table ip6 nat is managed by iptables-nft, do not touch!
```


# Summary Report
- Docker uses Linux's netfilter framework (_`iptables`_) to manage container network isolation and port forwarding,
- When Docker is installed with its default settings, it directly manipulates `iptables` rules to create network bridges, forward container traffic, and allow access to published container ports.
- Firewalld is a front-end manager for iptables zones and rules to simplify firewall configurations based on network zones/interfaces.
- Firewalld zone rules apply based on interface assignment.
- Docker's iptables rules allow published ports from containers to be accessed even if those ports are not open in firewalld's zone configuration for the host interface.
- This can cause confusion, where port access appears possible despite firewall rules seemingly blocking them.