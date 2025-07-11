+++
date = '2025-03-08T17:00:31+05:30'
draft = false
title = 'Setup: A beginner `pi-hole` setup (Manual DNS Allocation for Each Device)'
tags = [ "docs", "setup", "network"]

+++

I don't have enough knowledge about this when writing the documenation for setting up the `pi-hole`. I'm running this on a 
container in **Raspberry Pi 5 8GB** device (I know it's an overkill but it's for learning).

# Let's use `docker compose` to run the `pi-hole` container
  - Create directory for storing `pi-hole` configurations
```sh
mkdir pihole-config
cd pihole-config
```
  - Create a file named `docker-compose.yml`
```sh
touch docker-compose.yml
```
  - Copy & paste this `YAML` configuration into `docker-compose.yml`
```yml
services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp" # DNS Ports
      - "53:53/udp" # DNS Ports
      - "80:80/tcp" # Default HTTP Port
      - "443:443/tcp" # Default HTTPs Port
      - "67:67/udp" # using Pi-hole as your DHCP server
    environment:
      TZ: 'Asia/Kolkata'
      # Not setting one will result in a random password being assigned
      FTLCONF_webserver_api_password: 'pass'
      # If using Docker's default `bridge` network setting the dns listening mode should be set to 'all'
      FTLCONF_dns_listeningMode: 'all'
    volumes:
      # Pi-hole's databases and common configuration file
      - './etc-pihole:/etc/pihole'
      # Uncomment the below if you have custom dnsmasq config files that you want to persist. Not needed for most starting fresh with Pi-hole v6. If you're upgrading from v5 you and have used this directory before, you should keep it enabled for the first v6 container start to allow for a complete migration. It can be removed afterwards. Needs environment variable FTLCONF_misc_etc_dnsmasq_d: 'true'
      #- './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      # See https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
      # Required if you are using Pi-hole as your DHCP server, else not needed
      - NET_ADMIN
      # Required if you are using Pi-hole as your NTP client to be able to set the host's system time
      - SYS_TIME
      # Optional, if Pi-hole should get some more processing time
      - SYS_NICE
    restart: unless-stopped
```
  - Run the `docker compose`
```sh
docker compose up -d
``` 
  - Head inside the weblogin. i.e, http://localhost:80/admin (**Note**: Make sure the URL ends with `/admin`)

# Let's setup the DNS address and connect other devices to use `pi-hole`
- Login into the `pi-hole` web login
- Goto **Settings** >> **DNS** > Click the DNS of your liking in _IPV4_(I chose Open DNS)

# Let's Connect from a Device
1. `pi-hole` installed in `192.168.31.102`, so make sure to point the devices to this IP address.
2. Change the DNS manually on a specific connection.
```sh
sudo nmcli connection modify device-name ipv4.dns "192.168.31.102"
sudo nmcli connection reload
sudo nmcli connection up device-name
```
Check whether it's actually allocated to the device
```sh
sudo nmcli connection show device-name | grep DNS
```
- When the specified DNS is allocated, let's check the `resolv.conf` as well
```sh
cat /etc/resolv.conf
```
- Manually change the configuration, when the specified DNS address is not reflected here
```sh
nameserver 192.168.31.102
```

# One-Step Installation (with `docker` installed in linux)
```sh
mkdir pihole-config
cd pihole-config
touch docker-compose.yml
echo "services:
  pihole:
    container_name: pihole
    image: pihole/pihole:latest
    ports:
      - "53:53/tcp" # DNS Ports
      - "53:53/udp" # DNS Ports
      - "80:80/tcp" # Default HTTP Port
      - "443:443/tcp" # Default HTTPs Port
      - "67:67/udp" # using Pi-hole as your DHCP server
    environment:
      TZ: 'Asia/Kolkata'
      # Not setting one will result in a random password being assigned
      FTLCONF_webserver_api_password: 'pass'
      # If using Docker's default `bridge` network setting the dns listening mode should be set to 'all'
      FTLCONF_dns_listeningMode: 'all'
    volumes:
      # Pi-hole's databases and common configuration file
      - './etc-pihole:/etc/pihole'
      # Uncomment the below if you have custom dnsmasq config files that you want to persist. Not needed for most starting fresh with Pi-hole v6. If you're upgrading from v5 you and have used this directory before, you should keep it enabled for the first v6 container start to allow for a complete migration. It can be removed afterwards. Needs environment variable FTLCONF_misc_etc_dnsmasq_d: 'true'
      #- './etc-dnsmasq.d:/etc/dnsmasq.d'
    cap_add:
      # See https://github.com/pi-hole/docker-pi-hole#note-on-capabilities
      # Required if you are using Pi-hole as your DHCP server, else not needed
      - NET_ADMIN
      # Required if you are using Pi-hole as your NTP client to be able to set the host's system time
      - SYS_TIME
      # Optional, if Pi-hole should get some more processing time
      - SYS_NICE
    restart: unless-stopped" >> docker-compose.yml
docker compose up -d
```
