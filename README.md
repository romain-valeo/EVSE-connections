# How to configure your EVSE connections

| Author                 | Modification        | Date      |
| ---------------------- | ------------------- | ------------------- |
| Romain MUNIER          | Initial Doc         | 14/11/24            |

This guide explains how to configure EVSE connections with different usecases.

# 1. Nominal devices configuration
Below is the recommended configuration of interfaces for both the Linux PC and the Phytec board:
![set-up](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-set-up.jpg "set-up")

## On the PC
On the PC, the main ethernet port (on the right-hand side) shall be let with Ubuntu default settings (dynamic, DHCP), in order to be connected to the Valeo network or any router.\
The name of this port generally starts with "enp0".\
\
Another ethernet channel can be opened using a USB/ethernet converter (either USB-A or USB-C).\
When connected to a device, it appears on command `ip a` with a name starting by "enx" (and "Ethernet USB" by Ubuntu).\
In Ubuntu settings, this connection shall be configured as static, with IPv4 address being 192.168.10.1 and mask being 255.255.255.0. No IPv6 configuration required.\
![ubuntu-settings](https://github.com/user-attachments/assets/7633092e-c127-4a0c-b518-83eb4ea7439e)
```
l-rmunier@pon1-l13395:~$ ip a
[...]
2: enp0s31f6: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:2b:67:ba:83:ad brd ff:ff:ff:ff:ff:ff
    inet 10.25.44.214/22 brd 10.25.47.255 scope global dynamic noprefixroute enp0s31f6
       valid_lft 689207sec preferred_lft 689207sec
    inet6 fe80::6224:3bec:f18a:d1b0/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
[...]
1156: enxf8e43b040da9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether f8:e4:3b:04:0d:a9 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.1/24 brd 192.168.10.255 scope global noprefixroute enxf8e43b040da9
       valid_lft forever preferred_lft forever
    inet6 fe80::656f:45a9:e196:8ec/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```
> [!NOTE]  
> Connection to the Valeo Wifi network is disabled. Hence, only wired connection is possible.

## On the Phytec board
On the Phytec board, if [set-up instructions](https://github.com/deniz-valeo/deploy-redpeskOS-phytec-guide) have been followed correctly, no additional configuration is needed.\
eth0 is the one port next to the USB ports, eth1 is the other one.\
eth0 port is configured as static with IPv4 address 192.168.10.3.\
eth1 port is configured as dynamic (DHCP).\
eth2 is an additional port not visible, which is used for the communication with the PLC module (Codico). No configuration of this port is required.
```
[root@tux-evse-valeo36 ~]# ip a
[...]
3: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 28:b5:e8:e2:55:33 brd ff:ff:ff:ff:ff:ff
    inet 192.168.10.3/24 brd 192.168.10.255 scope global noprefixroute eth0
       valid_lft forever preferred_lft forever
4: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether be:d9:4a:fb:c9:10 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.104/24 brd 192.168.1.255 scope global dynamic noprefixroute eth1
       valid_lft 84242sec preferred_lft 84242sec
    inet6 fe80::ff8a:f213:b2fb:e56f/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
5: eth2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 100
    link/ether 96:c4:b5:a2:11:50 brd ff:ff:ff:ff:ff:ff
[...]
```
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
Plug the wifi dongle in one of the USB ports.\
If you use a smartphone, open wifi access point on it.\
From the Phytec board, scan the available Wifi networks : `nmcli device wifi list`.\
You may have to try several times, maybe even unplug/plug the dongle to make it work properly.\
Once you detect the networks, connect to the wanted wifi network: `nmcli device wifi connect "router_ssid" --ask`

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
Then turn off and turn on the connection to take into account the changes.\
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
# 4. Configuration for ISO15118 testing with the Trialog ComboCS (point-to-point connection)
If you want to run tests with the Trialog ComboCS with no router or any other complex configuration, you can configure the environment as defined here:\
![isop2p](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-iso-testing-point-to-point.jpg "isop2p")\
From initial configuration of the Phytec board, nothing to be done on the board itself.\
Plug the ethernet cable on the eth0 port (address 192.168.10.3).\
\
On the PC, configure:\
-the wired connection (enx, USB ethernet) with static address 192.168.10.1 and mask 255.255.255.0.\
-the wired connection (enp0, PCI ethernet) with static address 192.168.37.10 and mask 255.255.255.0.\
\
Deactivate and activate both connections.\
\
Then connection to the Phytec board should work : `ssh root@192.168.10.3` from the PC.\
To open the ComboCS UI, open a brower and type `http://192.168.37.167/ui`.

# 5. Configuration for ISO15118 testing with the Trialog ComboCS (with LAN connection, and SLAC bypassed)
![iso-lan-slac](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-iso-testing-lan-slacbypassed.jpg "iso-lan-without-slac")

# 6. Configuration for ISO15118 testing with the Trialog ComboCS (with LAN connection, and SLAC activated)
![iso-lan-slac](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-iso-testing-lan-slac.jpg "iso-lan-slac")

# 7. Full remote configuration
Below is the recommeneded configuration for all connections between PCs, boards and the ComboCS.\
As it is, any device can be reached remotely.\
It fully relies on routers, DHCP mechanisms and VPN static addresses assigned to every device.
![fullremote](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-full-remote.jpg "fullremote")\
As described in Phytec set up tutorial, each board shall have installed a VPN configuration file (with a specific IP address) and the VPN shall be activated.\
Then, all boards can be connected to a router (at the office or at home) via the eth1 (DCHP) port.\
All PCs shall have their VPN configuration files installed and the VPN activated also. They also have to be connected to the internet (possible through the Valeo network).\
\
After that, any board is reachable from any place using its VPN IP address: eg. `ssh root@10.52.253.36`.\
\
To command the ComboCS remotely, we need to use a SSH tunnel, via Phytec connected to it.\
To open the ComboCS UI, first open the SSH tunnel: \
-From the PC, launch the following command:
`ssh -L 15108:192.168.1.111:15108 -L 15110:192.168.1.111:15110 -L 15200:192.168.1.111:15200 -L 15111:192.168.1.111:15111 -L 39645:192.168.1.111:39645 -L 8080:192.168.1.111:80 root@10.52.253.36`
> [!NOTE]  
> In the previous command:\
> Replace 10.52.253.36 by the VPN IP address of the board used for the tunnel (one in the same router network).\
> Replace 192.168.1.111 by the local IP address of the ComboCS assigned by the router (most of the time, address will 192.168.1.111).\
> To obtain it, you can either open the router admininstration UI to check all connected devices,\
> or you can type `ip neigh show` from the Phytec board terminal to identify all other devices IP addresses in the same network.

Once done, you can open a brower and type `http://localhost:8080/ui/index.html` to open the ComboCS UI.
