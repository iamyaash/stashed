+++
date = '2025-02-08T21:06:31+05:30'
draft = false
title = ' DNS Change & Troubleshoot (common)'
tags = [ "docs", "network", "script"]

+++

# Changing DNS Settings
In order to change the DNS settings, we can choose two ways
1. Hard way (*using CLI, actually that's easy*)
2. Easy way (*using GUI, kind of lame XD*)

But let's do it the hard way, which is more rewarding than using GUI. I particularly don't have any steps to mention but let's do it with a scenario which can be easy to understand.
> You just installed a new operating system and you have to change the DNS settings.

## Check the current DNS settings
The DNS addresses are managed by `nmcli` and `systemd-resolved`, so be ready to see some errors when making changes using CLI.
Let's look at the current DNS settings,
```sh
cat /etc/resolve.conf
```
```
#output looks something similar like in the last few lines
127.0.0.27 #this is default DNS assigned by the OS
```

## Let's change the DNS address
We can use custom DNS addresses for each network that we connect. But let's no do this and changes the DNS settings for the whole system.

1. Show the available network connections
```sh
sudo nmcli connection show
```
```sh
NAME             UUID                                  TYPE      DEVICE     
mobile-ethernet  c6cc9a1a-958c-3e4e-81b6-a52c14534012  ethernet  enp5s0f4u1 
JioFi2_A62824    12ce6f07-a15d-40b0-b79c-e094746ea663  wifi      wlp3s0     
local-eth        4e0de5ff-2812-480f-b7fd-6aaaeef0eb95  ethernet  enp2s0     
lo               f48ff59d-7039-4fce-9679-c84e4ee76aaa  loopback  lo         
docker0          323d8d63-8367-4119-ae4f-24598c0641c1  bridge    docker0    
SO-05K           4908ce9f-07dd-4ad1-a0aa-de7a1a9ad630  wifi      --         
```

2. Change the DNS address of the network you want use:
```
sudo nmcli connection modify mobile-ethernet ipv4.addresses "192.168.1.1,8.8.8.8,8.8.4.4"
```
This will change the DNS address of the network you modified now.

3. Let's reload the connection:
Just to make sure everything is loaded and the changes are reflected

```sh
sudo nmcli connection reload
```

4. Run the network you just modified:
```sh
sudo nmcli connection up mobile-ethernet
```

5. Make sure the network device is assigned the DNS address you just modified:
```sh
sudo nmcli connectio show mobile-ethernet | grep DNS
```

6. Check the changes are reflected to `resolve.conf`
```sh
cat /etc/resolve.conf
```
Well, this should do it!
If not, let's debug it :)

1. Use `resolvectl status` to check the status:
```sh
resolvectl status
```

2. Let's remove and restart some stuffs:
- First let's remove the `/etc/resolve.conf`
```sh
sudo rm /etc/resolve.conf
```
- Let's restart `systemd-resolved`
```sh
sudo systemctl stop systemd-resolved
sudo systemctl disable systemd-resolved
```
```sh
sudo systemctl enable systemd-resolved
sudo systemctl start systemd-resolved
```
```sh
sudo systemctl restart NetworkManager
```
3. One last check:
```
cat /etc/resolve.conf
```

Well this should definitely fix the DNS. Else, the problem might be different!

> **Note:** We are making the changes to reflected globally across the machine and not bound to the specific device.
