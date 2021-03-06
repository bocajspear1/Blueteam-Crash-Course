---
layout: default
title: Chapter 4 - More Networking
---

# Assumptions

* You read the last chapter
* You have a basic understanding of IP addresses and subnet masks.

# More Networking

Since networking is so critical to managing your environment, here's another chapter.

# What You'll Learn

* Routing Basics 
* NAT 
* ARP
* Setting Addresses

# Routers and Routes

Most networks will have at least one router. A router can be a Cisco device, a small home router, or even a linux box with more than one network interface. It does what the name suggests, and helps route stuff to its destination. 

A network needs a router because it is implausible that device will know how where to send data to every other system in the world, so this job is passed off into a series of routers. These routers know where to send data for certain destinations or know who to hand it off to if they don't. 

## Default Gateway

When any system doesn't know where to send some data, it will refer to its "default gateway." Systems will need to configure their default gateway or get it from DHCP.

Setting the default gateway manually:
* [Centos](https://coderwall.com/p/iqtvhw/default-gateway-on-centos)
* [Ubuntu <16.04](https://www.cyberciti.biz/faq/howto-debian-ubutnu-set-default-gateway-ipaddress/)
* [Ubuntu >=18.04](https://linuxconfig.org/how-to-configure-static-ip-address-on-ubuntu-18-04-bionic-beaver-linux)

## Routing Table

A system will determine this by looking at its "routing table." This holds a map of what paths to destinations the system knows about. The routing table will always have networks the system is directly connected to (all its local networks) because it obviously knows where to send data for those networks, just out the interface connected to that local network. Routing tables can also be given "routes" by manual insertion or by "routing protocols" that transmit paths to destinations. These routes tell the system who to hand off data for a certain destination to. Routes also have subnet masks, which is used by the system to determine if a packet is bound for that network or not.

A system's routing table can be viewed on a system using the `route` or `ip route`(Linux only) command.

Linux:
```
jacob@web:~$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.16.2.1      0.0.0.0         UG    0      0        0 ens3
172.16.2.0      0.0.0.0         255.255.255.0   U     0      0        0 ens3
```
OR
```
jacob@web:~$ ip route
default via 172.16.2.1 dev ens3 onlink
172.16.2.0/24 dev ens3  proto kernel  scope link  src 172.16.2.3
```

Windows:
```
PS C:\Users\jacob> route print
===========================================================================
Interface List
 16...4c cc 6a dc 5c 8b ......Qualcomm Atheros AR8171/8175 PCI-E Gigabit Ethernet Controller (NDIS 6.30) #2
...
 23...30 e3 7a ee 7d c5 ......Intel(R) Dual Band Wireless-AC 3168 #2
  1...........................Software Loopback Interface 1
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0      192.168.6.1    192.168.6.139     25
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
...
===========================================================================
```

## Routing Process

When a system wants to send something, it'll go through a process like this:

* Check the IP it wants to go to against the routing table.
  * If it has the network in the routing table, and its a local network, it will send it to the device on that local network.
  * If it has the network in the routing table, and its a route, it will pass the data on to the other device in the route
  * If it does NOT have the network in the routing table, it will pass it off to the default gateway.

Essentially, this process happens until the data reaches its destination. Then the destination has to do the exact same process again, just in reverse. 

![BlueTeam!](images/blueteam.png) Routing is a two-way street, you must know how to get there, but they also need to know how to get back!

# Network Address Translation (NAT)

Network Address Translation (NAT) is a widely used process on converting addresses from private IP addresses, which can't be routed on the internet, to public IP addresses, which can be routed on the internet. (If you've used a home router, you've used NAT before.) This is why your IP address that appears in sites like https://www.whatismyip.com/ might be different than the address you get when running `ipconfig` or `ifconfig`.

# ARP

ARP is a protocol that is used on a local network to find MAC addresses for a given IP address. A system will use ARP request to ask which system has a given IP and what their MAC address, so the system can send stuff on layer 2(Data Link Layer). The system that has that IP will response with an ARP response.

> ![BlueTeam!](images/redteam.png) This protocol has no verification mechanism, so any system can pretend to be another system by responding to the ARP request. Using tools such as Ettercap, the red team can intercept local network traffic (only on a local network) by pretending to be the destination system. Lookup "ARP man-in-the-middle-attacks" and Ettercap for more info.

# Giving a machine a IP Address

To communicate, we need to give our systems IP addresses. This can be done in two ways.

## DHCP

DHCP is a network protocol that allocates and communicates IP addresses to systems. A device will send out a message indicating they want an address, and the DHCP server will respond, assigning them an IP address, which is the device's responsibility to assign to itself, and providing the default gateway, network DNS servers and other network information. DHCP records these address allocations and makes sure nobody gets a duplicate IP address.

DHCP is usually used by desktop and workstations. Servers, like the ones you will be defending, will not use DHCP, and you must assign them their address.

## Static IPs

Static IPs are when you manually give a system an IP address. ![BlueTeam!](images/blueteam.png) This means you have to track your addresses yourself. 

> ![BlueTeam!](images/blueteam.png) A system will NOT usually check if they have been given an static IP address that is already been given out. If you are getting intermittent connection problems, you may have allocated the same IP address on two systems. To test this, you can use the `arping` tool to see if two systems respond to your ARP message. See [here](http://linux-ip.net/html/tools-arping.html) and [here](https://linux.die.net/man/8/arping) for more info.


Here are some guides on how to set static IPs on a few Linux types:

* [CentOS 7](https://www.itzgeek.com/how-tos/linux/centos-how-tos/how-to-configure-static-ip-address-in-centos-7-rhel-7-fedora-26.html)
* [Ubuntu <=16.04](https://www.howtoforge.com/tutorial/howto-set-a-static-ip-on-ubuntu/) (Note you can also put `dns-nameservers <DNS_IP>` after the `gateway` line in `/etc/network/interfaces` to set the DNS server.)
* [Ubuntu 17.04+](https://www.tecmint.com/configure-network-static-ip-address-in-ubuntu/)

|         |  Navigation  |   |
| :-------------: |:-------------:| -----:|
| [< Ch 3](Chapter3-NetworkingBasics) | [Home](index) | [Ch 5 >](Chapter5-BeingAnAdmin)  |



{% include footer.html %}