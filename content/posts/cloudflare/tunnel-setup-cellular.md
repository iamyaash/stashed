---
date: '2026-02-25T16:31:44+05:30'
draft: true
title: 'Cloudflare Tunnel Setup On Airfiber Internet'
summary: "If you own an Airfiber connection, then you most probably have trouble hosting your application on public domain. Airfiber depends on cellular internet connection, hence you won't be provided with static IP. That's where Cloudflare tunnel comes in to save you!"
tags:
- Cloudflare
- Tunnel
- Airfiber/Cellular Internet
- Self Host
author: "Yashwanth Rathakrishnan"
ShowToc: true
TocOpen: true 
ShowReadingTime: true
ShowBreadCrumbs: true
ShowCodeCopyButtons: true
cover:
    image: "posts/cloudflare/img/cloudflare-tunnel.png"
    alt: "Cloudflare Tunnel"
---

# Why Airfiber?
I'm living in a remote area, and getting internet connection is impossible. Fortunately, I was able to get an [Airfiber](https://www.jio.com/airfiber/) connection from [Jio](https://www.jio.com/), and I have been using that as my primary internet source for a while. I started using Raspberry Pi, broke some things and I have been using locally hosted services, then I got a free domain name using my college student email, and I decided to host my own services on public domain only to find out that I need static IP to host applications. Which is not possible, if you are using cellular internet connection, because it changes frequently.

I started digging into this issue, and that eventually led me to [Cloudflare tunnels](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/) which helped me host services even with Airfiber/Cellular internet connection. The process is straightforward and simple, but I did faced some errors in the beginning but I found a workaround to make it happen. This post will be about how to host services on public domain with the help of Cloudflare tunnels and let's get started!

# Getting Started
## Install `cloudflared`
- **Fedora/RHEL (`rpm`-based distros)**:
	1. Add Cloudflare's repository:
	```sh
	curl -fsSl https://pkg.cloudflare.com/cloudflared.repo | sudo tee /etc/yum.repos.d/cloudflared.repo
	```
	2. Install `cloudflared`:
	```sh
	sudo yum update && sudo yum install cloudflared
	```

- **Debian/Ubuntu (`apt`-based distros)**:
	1. Add Cloudflare's package signing key:
	```sh
	sudo mkdir -p --mode=0755 /usr/share/keyrings
	curl -fsSL https://pkg.cloudflare.com/cloudflare-public-v2.gpg | sudo tee /usr/share/keyrings/cloudflare-public-v2.gpg >/dev/null
	```
	2. Add Cloudflare's `apt` repo to your `apt` repositories`:
	```sh
	echo "deb [signed-by=/usr/share/keyrings/cloudflare-public-v2.gpg] https://pkg.cloudflare.com/cloudflared any main" | sudo tee /etc/apt/sources.list.d/cloudflared.list
	```
	3. Install `cloudflared`:
	```sh
	sudo apt-get update && sudo apt-get install cloudflared

	```

- **Arch** `pacman -Syu cloudflared`
- [**Others** such as pre-built binaries, Docker, Windows, mac-OS and more](https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/downloads/).

## Create Cloudflare Tunnel
1. Login into your Cloudflared account using `cloudflared`:
```sh
cloudflared tunnel login
```
- Running this command will prompt you the link to log into your Cloudflared account and also opens the default browser with the provided link.
- Also, it generates an account certificate, which will be used for cloudflared authentication purposes.
2. Create a tunnel
```sh
cloudflared tunnel create <TUNNEL_NAME>
```
*Sample Output*:
```sh
iamyaash-lubuntu@lubuntu:~$ cloudflared tunnel create l-tunnel
Tunnel credentials written to /home/iamyaash-lubuntu/.cloudflared/75c13246-0467-4f74-98c6-494207162f91.json. cloudflared chose this file based on where your origin certificate was found. Keep this file secret. To revoke these credentials, delete the tunnel.

Created tunnel l-tunnel with id 75c13246-0467-4f74-98c6-494207162f91
```
> **Copy the UUID** because you'll need it later!
- Running this command creates credential file into `~/.cloudflared/*.json` (_default directory_)
- Create a tunnel by establishing a persistent relationship between the name you provide and a UUID for your tunnel. 
- But there's no connection is active within the tunnel yet. Because, we need to configure the tunnel before activating any connection.

3. Verify the tunnel has been created successfully (_Optional_)
```sh
cloudflared tunnel list
```

## Configure Cloudflare Tunnel
1. Create a `config.yml` inside the `~/.cloudflared/` directory
```sh
touch ~/.cloudflared/config.yml
```
2. Add these lines into `config.yml`:
```sh
nvim config.yml
```
```yaml
tunnel: <UUID>
credentials-file: /home/<username>/.cloudflared/<TUNNEL_ID>.json
origincert: /home/<username>/.cloudflared/cert.pem


ingress:
  # Rules map traffic from a hostname to a local service:
  - hostname: <SUB-DOMAIN>.<DOMAIN_NAME>
    service: http://localhost:8088

  - service: http_status:404
```
- Paste the tunnel id you copied before in `<UUID>`.
- Point both the `credentials-file` & `origincert` to the correct location.
- Ensure, you enter the right sub-domain & domain name to the application you would like to host.

# Start Routing Traffic (Public Network Only)
Assign a [`CNAME`](https://www.cloudflare.com/learning/dns/dns-records/dns-cname-record/) record that points traffic to your tunnel subdomain:
```sh
cloudflared tunnel route dns <UUID or Tunnel Name> <hostname>
```
> CNAME (**Canonical Name**)record is a type of DNS record that maps an alias domain name `blog.ex.com` to a "canonical" or true domain such as `ex.com`. It simply points to the same destination without using an IP address, as they pointing to the same domain address.

Example: 
```sh
cloudflared tunnel route dns airfiber-tunnel jellyfin
```
# Run `cloudflared` Tunnel
## Run as Native Tunnel
- Run the tunnel normally like this:
```sh
cloudflared tunnel run <UUID or Tunnel Name>
```
- In case you a custom configuration file, in a different location, then execute this command:
```sh
cloudflared tunnel --config /path/to/your/custom/config.yml run <UUID or Tunnel Name>
```

## Run as Systemd Service
Ensure `cloudflared` tunnel configurations exist inside the `~/.cloudflared/`.
- Install `cloudflared` service:
```sh
cloudflared service install
```
```sh
# Use this command, in case it shows error incorrect location of config.yml
sudo cloudflared --config /home/USERNAME/.cloudflared/config.yml service install
```
 
- Start the service:
```sh
sudo systemctl start cloudflared
```
- Verify whether the service is running or not:
```sh
sudo systemctl status cloudflared
```
- Ensure the service is enabled, else it won't start after boot:
```sh
sudo systemctl enable cloudflared
```
- Restart the service every time you make changes to `~/.cloudflared/config.yml`:
```sh
sudo systemctl restart cloudflared
```
