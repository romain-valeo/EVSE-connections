# How to configure your EVSE connections?

| Author                 | Modification        | Date      |
| ---------------------- | ------------------- | ------------------- |
| Romain MUNIER          | Initial Doc         | 14/11/24            |

This guide explains how to configure EVSE connections with different usecases.\

> [!NOTE]  
> Nothing here so far

# Nominal devices configuration:
Below is the recommended configuratoin of interfaces for both the Linux PC and the Phytec board.
![set-up](https://github.com/romain-valeo/EVSE-connections/blob/main/Phytec-connections-set-up.jpg "set-up")

>>On the PC
On the PC, the main ethernet port shall be let with automatic settings (DHCP), in order to be connected to the Valeo network or any router.
The name is port generally starts with "enp0".
> [!NOTE]  
> Connection to the Wifi Valeo network is disabled. Hence, only wired connection is allowed and possible.

Another ethernet channel can be opened using a USB/ethernet converter (either USB-A or USB-C).
When connected to a device, it appears on command 'ip a' with a name starting by "enx".
This port shall be configured as static in Linux settings, with IPv4 address being 192.168.10.1 and mask being 255.255.255.0. No IPv6 configuration required.

>> On the Phytec board
On the Phytec board, if set-up instructions have followed, no additional configuration is needed.
eth0 port is configured as static with IPv4 address 192.168.10.3
eth1 port is configured as dynamic (DHCP).
