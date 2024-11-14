# How to configure your EVSE connections?

| Author                 | Modification        | Date      |
| ---------------------- | ------------------- | ------------------- |
| Romain MUNIER          | Initial Doc         | 14/11/24            |

This guide explains how to configure EVSE connections with different usecases.

> [!NOTE]  
> Nothing here so far

# 1. Nominal devices configuration:
Below is the recommended configuratoin of interfaces for both the Linux PC and the Phytec board:
![set-up](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-set-up.jpg "set-up")\

## On the PC
On the PC, the main ethernet port (on the right-hand side) shall be let with Ubuntu default settings (dynamic, DHCP), in order to be connected to the Valeo network or any router.
The name is port generally starts with "enp0".

> [!NOTE]  
> Connection to the Wifi Valeo network is disabled. Hence, only wired connection is possible.

Another ethernet channel can be opened using a USB/ethernet converter (either USB-A or USB-C).\
When connected to a device, it appears on command 'ip a' with a name starting by "enx".\
This port shall be configured as static in Linux settings, with IPv4 address being 192.168.10.1 and mask being 255.255.255.0. No IPv6 configuration required.

## On the Phytec board
On the Phytec board, if set-up instructions have been followed correctly, no additional configuration is needed.\
eth0 is the one port next to the USB ports, eth1 is the other one.\
eth0 port is configured as static with IPv4 address 192.168.10.3.\
eth1 port is configured as dynamic (DHCP).\
eth2 is an additional port not visible, which is used for the communication with the PLC module (Codico). No configuration of this port is required.

# 2. How to connect the board to the internet:
There are several possibilities how to connect the board to the internet.

## 2a. With a 4G dongle
![4g-dongle](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-USB-4G-dongle.jpg "4g-dongle")\
Plug the 4G dongle in one of the USB port.\
Ubuntu will recognize the 4G dongle and internet connection will be set up.\
Check by ping: 
```
ping 8.8.8.8
```
> [!NOTE]  
> With our current dongle, this connection works randomly.

## 2b. With a smartphone as a router (USB)
![phone-USB](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-USB-phone.jpg "phone-USB")\
Plug your smarthone in one of the USB port of the Phytec.\
Configure your phone as Access Point by USB (share internet access).\
Ubuntu will recognize your phone as a router and internet connection will be set up.\
Check by ping: 
```
ping 8.8.8.8
```

## 2c. With a smartphone as a router (wifi)
![phone-wifi](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-wifi-phone.jpg "phone-wifi")\
Plug the wifi dongle in one of the USB port.\
Scan the available Wifi networks : nmcli device wifi list.\
If you want to use a smartphone, open wifi access point on it.\
Connect to the wifi network:
```
nmcli device wifi connect "network_ssid" --ask
```
> [!NOTE]  
> "network_ssid" can refer to any smartphone or router you want to connect to.\
Enter the password.\
If successful, internet connection will be set up.\
Check by ping: 
```
ping 8.8.8.8
```

## 2d. With a wired connection to a router
![router-eth](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-eth-router.jpg "router-eth")\
Connect your router to the board via eth1 port.\
Ubuntu will recognize the connection and internet connection will be set up.\
Check by ping: 
```
ping 8.8.8.8
```
