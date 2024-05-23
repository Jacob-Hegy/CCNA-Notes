# Common Terminologies
### Star Topology
- When several devices all connect to one central device
![[Star Topology.png]]

### Full Mesh
- When each device is connected to each other devices
![[Full Mesh Topology.png]]

### Partial Mesh
- When some devices are connected to each other, but not all
![[Partial Mesh Topology.png]]

# 2-Tier & 3-Tier
## 2-Tier:
- Consists of two hierarchical layers:
	1. Access Layer
		- The layer end hosts connect to
		- Typically have Access Layer Switches configured for lots of hosts
		- QoS marking is done here
		- Security services are performed here (port security, DAI, DHCP Snooping, etc.)
		- Switchports might be PoE-enabled for wireless APs, IP phones, etc.
	2. Distribution Layer/Aggregation Layer
		- Aggregates connections from the Access Layer Switches
		- Typically is the border between Layer 2 and Layer 3 (Data Link and Network)
		- Used to connect to services like the Internet, WAN, etc.
- Also called the "collapsed core" design because it omits the Core Layer which is found in the 3-Tier design
- Example:
![[2-Tier Example.png]]

- The issue with a 2-Tier design is that when there are many distribution layer switches, the number of connections to create a full mesh between all the switches grows rapidly
	- This is where a Core Layer is added, forming a 3-Tier design (**Core Layer is recommended when there's more than three individual Distribution Layers**)
## 3-Tier:
- Has the Access Layer and the Distribution Layer as well as a **Core Layer**
- Core Layer:
	- Connects Distribution Layers together in large LAN networks
	- Focus on speed
		- Avoid CPU-intensive operations like security
		- All Layer 3 connections
		- Maintain connectivity throughout the LAN even if devices fail
![[3-Tier Example.png]]
# Spine-Leaf Architecture (Data Center/Cisco ACI)
- Intended for data centers
- Good for networks with high amounts of North-South (between different layers) traffic as well as East-West (between hosts in the same Access Layer) traffic
![[Spine-Leaf Example.png]]

- Rules:
	- Every Leaf switch connects to every Spine switch
	- Every Spine switch connects to every Leaf switch
	- Leaf switches do not connect to other Leaf switches
	- Spine switches do not connect to other Spine switches
	- End hosts only connect to Leaf switches
- The path taken by traffic is randomly chosen to balance the traffic according to load balancing performed amongst the Spine switches
- The point is that every server/end host is separated by the same number of hops, providing consistent latency for East-West traffic
# SOHO (Small Office/Home Office)
- Refers to a network for a small home office/small company with few devices
- The network doesn't have complex needs, so a single device is used for all networking needs (hence a home router)
- This home router serves as a:
	- Router
	- Switch
	- Firewall
	- WAP
	- Modem
