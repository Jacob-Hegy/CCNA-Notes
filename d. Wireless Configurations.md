# Topology

![[Jeremy's Wireless Network Topology.png]]


# Switch Configuration
```
Create the VLANs:
SW1(config)#vlan 10
SW1(config-vlan)#name Management
SW1(config-vlan)#vlan 100
SW1(config-vlan)#name Internal
SW1(config-vlan)#vlan 200
SW1(config-vlan)#name Guest

Configure the Access Ports (F0/6 will be used to gain access to WLC1's GUI)
SW1(config)#int range f0/6-8
SW1(config-if-range)#switchport mode access
SW1(config-if-range)#switchport access vlan 10
SW1(config-if-range)#spanning-tree portfast

Configure the EtherChannel/LAG (WLCs only support static LAG, no PAgP or LACP)
SW1(config-if-range)#interface range f0/1-2
SW1(config-if-range)#channel-group 1 mode on

Configure the Trunk Port to WLC1
SW1(config-if-range)#interface port-channel 1
SW1(config-if)#switchport mode trunk
SW1(config-if)#switchport trunk allowed vlan 10,100,200

Create the DHCP server for the APs (option 43 tells the APs the IP of their WLC)
SW1(config)#ip dhcp pool VLAN10
SW1(dhcp-config)#network 192.168.1.0 255.255.255.0
SW1(dhcp-config)#default-router 192.168.1.1
SW1(dhcp-config)#option 43 ip 192.168.1.100

SW1(config)#ip dhcp pool VLAN100
SW1(dhcp-config)#network 10.0.0.0 255.255.255.0
SW1(dhcp-config)#default-router 10.0.0.1

SW1(config)#ip dhcp pool VLAN200
SW1(dhcp-config)#network 10.1.0.0 255.255.255.0
SW1(dhcp-config)#default-router 10.1.0.1

Sets up SW1 as an NTP Server
SW1(config)#ntp master
```
# WLC Setup
- First, walk through the Wizard
```
Enable Link Aggregation (LAG) [yes][NO]: yes

Management Interface IP Address: 192.168.1.100
Management Interface Netmask: 255.255.255.0
Management Interface Default Router: 192.168.1.1
Management Interface VLAN Identifier (0 = untagged): 10
Management Interface DHCP Server IP Address: 192.168.1.1
```
- Note: the regulatory domain of the country specified on the AP must match that of the regulatory domain of the WLC; if not, the AP cannot join the WLC
# WLC Interface Configuration
## WLC Ports/Interfaces
- WLC **ports** are the physical ports that cables connect to
- WLC **interfaces** are the logical interfaces within the WLC (like the SVIs on a switch)
### Types of Ports:
#### Service Port
- Dedicated management port used for out-of-band management; must connect to a switch access port because it only supports one VLAN
#### Distribution System Port
- Standard network ports that connect to the "distribution system" (wired network) and are used for data traffic; usually connect to switch trunk ports and can form a [[11. EtherChannel|LAG/EtherChannel]]
#### Console Port
- Standard console port, either RJ45 or USB
#### Redundancy Port
- Port used to connect to another WLC to form a high availability (HA) pair
### Types of Interfaces:
#### Management Interface
- Used for management traffic such as Telnet, SSH, HTTP, HTTPS, RADIUS, NTP, Syslog, etc.
- CAPWAP tunnels also form to/from the WLC's management interface
#### Redundancy Management Interfaces
- When two WLCs are connected by their redundancy ports, one WLC is "active" and the other is "standby"; this inerface can be used to connect to and manage the "standby" WLC
#### Virtual Interface
- Used when communicating with wireless clients to relay DHCP requests, perform client web authentication, etc.
#### Service Port Interface
- This interface is bound to the service port if it's used; used for out-of-band management
#### Dynamic Interface
- Used to map a WLAN to a VLAN
	- Ex. Traffic from the internal WLAN will be sent to the wired network from the WLC's "internal" dynamic interface

- Web Authentication: after the client gets an IP address and tries to access a webpage, they will have to enter a username and password to authenticate
- Web Passthrough: web authentication, but no username or password; instead, a warning is displayed and the client must agree in order to access the Internet
- Conditional and Splash web redirect options are similar, but additionally require 802.1X Layer 2 Authentication
- QoS Settings: **Platinum (voice) > Gold (video) > Silver (best effort) > Bronze (background)**
# Lab Notes
- To configure interfaces, use the **Controller** tab
![[WLC Interface Configuration.png]]
- Port Number = the WLC's port connected to the switch
- VLAN Identifier = the VLAN the port is being used for
- IP Address & Netmask = IP & Netmask of the WLC within the VLAN
- Gateway = the switch's SVI for the VLAN
- Primary DHCP Server = IP of the DHCP server
- To configure WLANs, use the **WLANs** tab
