+++
date = '2025-02-23T18:16:08+05:30'
draft = false
title = 'Usage: `nmcli`'
+++

# What is `nmcli`?
The `nmcli` means **NetworkManager Command Line Interface**. It's command-line utility tool used for controlling `NetworkManager` and reporting network status.

_In simple terms, we use that to interact with network related devices._

## How to use it?

I don't have proper way to explain, but let's learn it with a case-scenario:

### Connect to a wifi network:
1. Turn on the `wifi` module:
```sh
sudo nmcli radio wifi on
```
2. List the available networks:
```sh
sudo nmcli device wifi list
```
In case, it didn't display any network, please `rescan` the available network and list it:
```sh
sudo nmcli device wifi rescan
sudo nmcli device wifi list
```

3. Let's connect to the device:
Let's take an example and connect to that device
```sh
IN-USE  BSSID              SSID           MODE   CHAN  RATE        SIGNAL  BARS  SECURITY 
*       C8:D7:79:A6:28:24  JioFi2_A62824  Infra  7     130 Mbit/s  86      ▂▄▆█  WPA2     
```

```sh
sudo nmcli device wifi connect JioFi2_A62824 password "enter your password here inside dquotes"
```