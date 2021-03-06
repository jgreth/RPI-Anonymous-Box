# RPI-Anonymous-Box
Resources necessary to set up a physically-isolated environment (using Raspberry Pi 3B) for anonymous internet usage.
The setup consists of two computers, one of them is a Raspberry Pi 3B connected to the Internet via WiFi, and a second computer which will be connected directly to the Pi by Ethernet. The second computer can't have any other network connectivity except for this Ethernet port.

The idea is to route everything from the Workstation to the Pi, which in turn will route everything through VPN, and then through Tor.

## Requirements
Hardware requirements:
- Separate PC/Laptop, remove WiFi adapter if present
- Raspberry Pi 3B
- 1x Ethernet cable
- 1x MicroUSB power supply for the Pi
- Spare keyboard/mouse for the Pi

Software requirements (for the Pi):
- isc-dhcp-server 
- tor
- openvpn
- iptables-persistent
- festival (optional) for voice information, it's pretty cool! If you don't want it, edit the script anon.sh.
- nyx (optional) for monitoring TOR usage

## Contents of the repository
- dhcpd.conf - configuration for the dhcp server, which provides a mini-network on the Ethernet
- anon.sh - this script will make everything easier. After configuring iptables and dhcpd you just need to type "anon start" to initialize the Network
- rules.v4 - these are iptables rules necessary to prevent any leaks
- torrc - TOR daemon settings

## OpenVPN setup
- My configuration is in list.txt. 
- Create a file auth.txt which contains 2 lines: your email address and passwordfor the VPN in /etc/openvpn/
- /etc/openvpn/ovpn_udp is the file where all your VPN configs should be located in

## What the script does
The script chooses a random VPN to connect to, connects to it, and starts the TOR daemon

## Guide
### For the Pi
- Replace all my IP addresses in configs etc. with your own
- Change the Pi password, apt update, apt upgrade etc.
- Install all the requirements
- Set the DHCP server up, configure it /etc/dhcpd/dhcp.conf
```
systemctl enable isc-dhcp-server
systemctl start isc-dhcp-server
````
- Connect via ethernet to the PC and check if you're in the network, there should be only 2 computers, your own and the Pi
- Configure OpenVPN and TOR (/etc/tor/torrc, /etc/openvpn)
- Disable TOR / OpenVPN daemon running at startup
```
systemctl disable tor
systemctl disable openvpn
````
- Load iptables rules to prevent leaks (copy the rules) 
- Disable IPv6 in the kernel (/etc/sysctl.conf and sudo sysctl -p after changing)
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```
- Enable IPv4 forwarding in the kernel 
```
net.ipv4.ip_forward = 1
```
- Reboot and check if everything is OK (you SHOULDN'T be able to connect to the internet except through the VPN)
- Move anon.sh to /usr/bin/anon for convenience and chmod +x
- sudo anon start
### For the PC
- Remove WiFi/Bluetooth/Camera/Microphone or disable in BIOS
- Install some kind of Linux for example Lubuntu with disk encryption
- Connect the ethernet cable to the PC
- **IMPORTANT** Install htpdate because TOR does not route UDP (NTP).
- Configure the TOR Browser not to use TOR. **TOR OVER TOR IS NOT RECOMMENDED**
- Go to check.torproject.org and fire up nyx (optional) on the Pi, to see if it works. Your route should look like this:

```PC -> Pi -> VPN -> Tor -> Destination server```

There's no way to SSH to the Pi on the Ethernet network/WiFi network as everything is transparently routed to port 9040 on the Pi.
You can even test it with nmap, it looks like every port is open and tcpwrapped.


## WARNING! YOU NEED TO REPLACE MY IP ADDRESSES WITH YOUR OWN !


## Gallery

![anon script](https://i.imgur.com/myR3AHC.jpg)


