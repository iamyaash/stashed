+++
date = '2025-01-30T20:58:55+05:30'
draft = false
title = ' Fix Wired-LAN Connection; when physically connected but no endpoint'
tags = [ "docs", "troubleshoot", "script" ]

+++

# Fixing Wired LAN

The scenario is very hard to explain, so I'll write down what happened and we'll relate them according to the situation. (**Yaash shrugs*).

1. Connection between my `m1` and `m2` has been severed. No matter what I do, it can't establish the connection with the `m2` from `m1`.
2. So, I deleted the network from my machine. When entering `nmcli con show`, it won't display the wired LAN.
3. Now, the cable is connected from both sides, meaning the devices are connected physically but the `ipv4.addresses` are not mapped properly.

## Steps:
### Check the `device status`
This command will display the devices that are connected to the machine. It can either be a **connected** or **disconnected** devices. Now we'll identify whether the cable is connected and the machine acknowledged it's connection.
```sh
nmcli device status
```
The output look something similar like this:
```sh
DEVICE          TYPE      STATE                   CONNECTION      
enp5s0f4u1      ethernet  connected               mobile-ethernet 
wlp3s0          wifi      connected               JioFi2_A62824   
lo              loopback  connected (externally)  lo              
p2p-dev-wlp3s0  wifi-p2p  disconnected            --              
enp2s0          ethernet  unavailable             --              
```
Now the `enp2s0` is the device that have physical connection but no ipv4 address is mapped in it. Let's fix it!

> If the device is not shown in this cmd, make sure the cable is connected properly and try this cmd again.

### Add a connection:
Now that we have the name of the device that has connection to the `m2` from `m1`. We have to `add` the device to the network. For that we need to add many arguements as well, they are:
- `con-name "ethernet"` - defines the names of the device.
- `type ethernet` - sets the type of the device to `ethernet`.
- `ipv4.method manual` - allows manual `ipv4` address mapping.
- `ipv4.addresses <ip.address here>` - manually entered IP address goes here.
- `ipv4.gateway` - default gateway address that be identified by using `ip route`. but mention gateway address of the `m2`.
- `ifname` - name of the device given by default.

Let's add the device now:

```sh
sudo nmcli connection add type ethernet con-name "ethernet" ifname enp2s0 ipv4.method manual ipv4.addresses 192.168.10.2/24 ipv4.gateway 192.168.10.1
```
This is where I messed, up so watch close 👀
- `ipv4.addresses 192.168.10.2/24` - indicates the machine we're currently using. (i.e `m1`)
- `ipv4.gateway 192.168.10.1` - indicates the machine that we want establish connection.

## Let's start the connection!
If you did everything right mentioned above, then you're good to go!
```sh
sudo nmcli connection up ethernet
```
