---
layout: post
title:  "Selective network routing via VPN"
date:   2020-04-25 13:00:00 +0530
categories: [Networking, Systemd, VPN, Routing]
comments: true
---

Everyone is working from home and for me it is remotely working on the servers in my college from home. I can only connect to them via VPN, but that slows down the my other browsing experience as well.
I was trying to figure out a way to route only the traffic of the server through the VPN and not use a VPN for the rest of the traffic. That's when I came to know about selective routing.

**At the end of this blog you'll be able to selective for which website or ip addresses you can route your vpn traffic through.**

Requirements:

- Ubuntu (Should work on other linux distros as well)
- [Openfortivpn](https://github.com/adrienverge/openfortivpn)

If you are using windows, you can try it with Windows Subsystem for Linux. Do [ensure that you have WSL2](https://github.com/microsoft/WSL/issues/4201).

## Steps for selective routing

### 1. VPN configuration file

This file tells our vpn client the specifics of our VPN and to not allow anything through our VPN for now. We'll use a script in the next section to whitelist the specific websites.

Save the below config file as vpn-config.conf anywhere on your computer

```
host = vpn.iiitd.edu.in
port = 10443
username = <your username>
password = <your pass>
set-routes = 0
set-dns = 0
pppd-use-peerdns = 0
```

Here is how my conf file looks like
```
host = vpn.iiitd.edu.in
port = 10443
username = <my username>
password = <my pass>
set-routes = 0
set-dns = 0
pppd-use-peerdns = 0
# X509 certificate sha256 sum, trust only this one!
trusted-cert = e46d4aff08ba6914e64daa85bc6112a422fa7ce16631bff0b592a28556f993db
```

### 2. Setup the PPP script

**What's PPP?**: PPP is Point to Point protocol. Linux uses this protocol to communicate over TCP/IP through your Internet Provider.[read more](https://docstore.mik.ua/orelly/linux/run/ch15_02.htm)

**What's pppd?** The PPP Daemon (pppd) is a freely available implementation of the Point-to-Point Protocol (PPP) that runs on many Unix systems. [read more](docstore.mik.ua/orelly/networking_2ndEd/tcp/appa_02.htm)

We are now going to write a script that will whitelist specific domains to pass through the VPN.

Use the following commands to create the script
```
sudo touch /etc/ppp/ip-up.d/fortivpn
sudo chmod a+x /etc/ppp/ip-up.d/fortivpn
```

**What's ip-up?** /etc/ppp/ip-up is a shell script executed by pppd when the link comes up. [read more](docstore.mik.ua/orelly/networking_2ndEd/tcp/appa_02.htm)

Edit that script with your favourite editor, it shall look like:
```
#!/bin/bash
#
# Whitelist here all domains that need to go through openfortivpn
# Domains are separated by a space
#
domains='example.com example.fr'
ips='192.168.0.15'

let resolved
for domain in $domains; do
    resolved=`dig +short $domain | tail -n1`
    ips="$ips $resolved"
done

for ip in $ips; do
    route add $ip dev ppp0
done
```

Here is my ppp script
```
#!/bin/bash
#
# Whitelist here all domains that need to go through openfortivpn
# Domains are separated by a space
#
ips='192.168.2.217 192.168.29.151'

let resolved
for domain in $domains; do
  resolved=`dig +short $domain | tail -n1`
  ips="$ips $resolved"
done

for ip in $ips; do
  route add $ip dev ppp0
done
```

### 3. Run the VPN!

The following command should connect you to your VPN now.
```
sudo openfortivpn -c vpn-config.conf
```
Below you can see the routes added for the ip addresses
ppp0 is the vpn and enp2s0 is the ethernet.
```
rohan@rohan-laptop ~> route                                                                  (base) 
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp2s0
one.one.one.one 0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
103.25.231.4    0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
link-local      0.0.0.0         255.255.0.0     U     1000   0        0 enp2s0
192.168.0.0     0.0.0.0         255.255.255.0   U     100    0        0 enp2s0
192.168.2.217   0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
192.168.29.151  0.0.0.0         255.255.255.255 UH    0      0        0 ppp0
```
That's about it. You can now work on your server and enjoy fast internet along!

Thanks for reading. If this did help you, feels free to like, comment and share this blog :)

### Bonus: Create a system service
It quite irritating to have a terminal open when you have connected to a VPN. Thereforce I created a system service to automatically connect to VPN on boot.

Run these commands to setup the service
```
sudo touch /etc/systemd/system/openfortivpn.service
```
Open it with your favourite editor and enter this configuration
<script src="https://gist.github.com/rohanrajpal/b443e20658cdf3e762a4e9df1a6e31bb.js"></script>
```
[Unit]
Description = OpenFortiVPN
After=network-online.target multi-user.target
Documentation=man:openfortivpn(1)

[Service]
User=root
Type=idle
ExecStart = /usr/bin/openfortivpn -c <path to your config file>
KillSignal=SIGTERM
StandardOutput=file:<any-place-where you want to save your logs>
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

Structure
// why would someone need it
// openfortivpn
// steps
// setup vpn
// setup pppd
// setup conf
// run the command
// bonus: create a system service