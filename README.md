# How to configure your EVSE connections

| Author                 | Modification        | Date      |
| ---------------------- | ------------------- | ------------------- |
| Romain MUNIER          | Initial Doc         | 14/11/24            |

This guide explains how to configure EVSE connections with different usecases.

> [!NOTE]  
> Nothing here so far

# 1. Nominal devices configuration
Below is the recommended configuratoin of interfaces for both the Linux PC and the Phytec board:
![set-up](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-set-up.jpg "set-up")

## On the PC
On the PC, the main ethernet port (on the right-hand side) shall be let with Ubuntu default settings (dynamic, DHCP), in order to be connected to the Valeo network or any router.\
The name of this port generally starts with "enp0".\
\
Another ethernet channel can be opened using a USB/ethernet converter (either USB-A or USB-C).\
When connected to a device, it appears on command 'ip a' with a name starting by "enx" (and "Ethernet USB" by Ubuntu).\
This connection shall be configured as static in Ubuntu settings, with IPv4 address being 192.168.10.1 and mask being 255.255.255.0. No IPv6 configuration required.

> [!NOTE]  
> Connection to the Wifi Valeo network is disabled. Hence, only wired connection is possible.

## On the Phytec board
On the Phytec board, if set-up instructions have been followed correctly, no additional configuration is needed.\
eth0 is the one port next to the USB ports, eth1 is the other one.\
eth0 port is configured as static with IPv4 address 192.168.10.3.\
eth1 port is configured as dynamic (DHCP).\
eth2 is an additional port not visible, which is used for the communication with the PLC module (Codico). No configuration of this port is required.

# 2. How to connect the board to the internet
There are several possibilities how to connect the board to the internet.\
This can be useful to update the packages and bindings installed on the board.

## 2a. With a 4G dongle
![4g-dongle](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-USB-4G-dongle.jpg "4g-dongle")\
Plug the 4G dongle in one of the USB port.\
Ubuntu will recognize the 4G dongle and internet connection will be set up.\
Check by ping: `ping 8.8.8.8`
> [!NOTE]  
> With our current dongle, this connection works randomly.

## 2b. With a smartphone as a router (USB)
![phone-USB](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-USB-phone.jpg "phone-USB")\
Connect your smarthone via one of the USB port of the Phytec.\
Configure your phone as Access Point by USB (share internet access).\
Ubuntu will recognize your phone as a router and internet connection will be set up.\
Check by ping: `ping 8.8.8.8`

## 2c. With a smartphone as a router (wifi)
![phone-wifi](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-wifi-phone.jpg "phone-wifi")\
Plug the wifi dongle in one of the USB port.\
If you use a smartphone, open wifi access point on it.\
Scan the available Wifi networks : `nmcli device wifi list`.\
You may have to try several times, maybe even unplug/plug the dongle to make it work properly.\
Connect to the wifi network: `nmcli device wifi connect "router_ssid" --ask`

> [!NOTE]  
> "router_ssid" can refer to any smartphone or router you want to connect to.

Enter the password.\
If successful, internet connection will be set up.\
Check by ping: `ping 8.8.8.8`

## 2d. With a wired connection to a router
![router-eth](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-eth-router.jpg "router-eth")\
Connect your router to the board via eth1 port.\
Ubuntu will recognize the connection and internet connection will be set up.\
Check by ping: `ping 8.8.8.8`

# 3. Configuration for static and local testing
This configuration is the simplest one for testing. It uses forced addresses on both sides to start communication between the PC and the Phytec board.\
![static](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-static-testing.jpg "static")\
Plug the ethernet cable on the eth0 port of the Phytec, and on the USB/eth converter of the PC.\
On the PC, on Ubuntu, configure the wired connection (USB ethernet) with static address 192.168.10.1 and mask 255.255.255.0.\
Then disable and enable the connection to take into account the changes.\
You can confirm the changes with the command `ip a` in the terminal of the PC.\
```
l-rmunier@pon1-l13395:~$ ip a
[...]
1156: enxf8e43b040da9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether f8:e4:3b:04:0d:a9 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.1/24 brd 192.168.10.255 scope global noprefixroute enxf8e43b040da9
       valid_lft forever preferred_lft forever
    inet6 fe80::656f:45a9:e196:8ec/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
[...]
```
\
On the Phytec board, from initial configuration, nothing to be done.\
You can confirm that the address 192.168.10.3 is assigned to the eth0 port with command `ip a` (when connected to board with the picocom for instance).\
```
[root@tux-evse-valeo36 ~]# ip a
[...]
3: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 28:b5:e8:e2:55:33 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.3/24 brd 192.168.10.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
[...]
```
Then connection should work : `ssh root@192.168.10.3` from the PC.
```
l-rmunier@pon1-l13395:~$ ssh root@192.168.10.3
root@192.168.10.3's password: 
Last login: Thu Nov 14 11:17:35 2024 from 10.52.253.11
[root@tux-evse-valeo36 ~]# 
```
