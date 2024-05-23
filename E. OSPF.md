- Open Shortest Path First
- Link State dynamic routing protocol (routers share info with each other, including routes and metric cost)
	- Each router has a "connectivity map" that they independently maintain and advertise to neighbors; each neighbor then uses this map to calculate the optimal route to each destination
	- More CPU and memory intensive, but reacts faster to network changes
- Uses Dijkstra's Algorithm
- Three versions:
	- OSPFv1 (1989): old not used
	- OSPFv2 (1998): used for IPv4
	- OSPFv3 (2008): used for IPv6 (can be used for IPv4, but OSPFv2 is more common)
- Routers store info about the network in link state advertisements (LSAs) which are organized in the link state database (LSDB)
	- Routers will flood LSAs until all routers in the OSPF area develop the same map of the network (LSDB)
## Areas
- Area = group of routers and links that share the same LSDB
- The backbone area (area 0) is an area that all other areas must connect to
- Routers with all interfaces in the same area are called internal routers
- Routers with interfaces in multiple areas are called area border routers (ABRs)
	- ABRs maintain separate LSDBs for each area they're connected to
- An autonomous system boundary router (ASBR) is an OSPF router that connects the OSPF network to an external network
- Routers connected to the backbone area are called backbone routers (routers can be ABRs/internal routers as well as backbone routers)
- Intra-area route = a route to a destination in the same OSPF area
- Interarea route = a route to a destination in a different OSPF area
- OSPF uses areas to divide up network
- Single-area good for small networks, but cause performance issues on larger networks \[thus, use more areas]
- Negative effects:
	- More time to calculate routes
	- Exponentially more processing power
	- larger LSDB takes up more memory
	- any small change cascades to all routers
- Rules:
	- Areas should be contiguous (all parts of an area must be connected, not divided up)
	- All OSPF areas must have at least one ABR connected to the backbone area
	- OSPF interfaces in the same subnet must be in the same area
## Metric
- Calculated based on the bandwidth of the interface
	- Divide the reference bandwidth by the interface's bandwidth
- Default reference bandwidth is 100 mbps
	- Reference: 100 mbps / Interface: 10 mbps = cost of 10
	- Reference: 100 mbps / Interface: 100 mbps = cost of 1
	- Reference: 100 mbps / Interface: 1000 mbps = cost of 1
	- Reference: 100 mbps / Interface: 10000 mbps = cost of 1
- All values less than 1 will be converted to 1
	- Thus FastEthernet, GigabitEthernet, and 10Gig Ethernet all have the same cost
- Three options to change the autocalculated value:
	1. Change the reference bandwidth
	2. Set the OSPF cost of the interface
	3. Change the bandwidth of the interface (changing bandwidth doesn't actually affect the speed of the interface; not recommended)
## Neighbors
- Ensuring routers become OSPF neighbors is the main task in configuring and troubleshooting OSPF
- Routers become neighbors by exchanging OSPF hello messages
	- Default hello timer is 10 seconds on an Ethernet connection
- Hello messages are multicast to 224.0.0.5 (multicast address for all OSPF routers)
	- Encapsulated in an IP header with a value of 89 in the Protocol field
- Neighbor states: ^849c32
	- **Down State**: the state in which a router doesn't know about any OSPF neighbors yet
	- **Init State**: the state in which the neighbor has noted router 1 (the original sender) in its OSPF neighbor table, but router 1 still hasn't found out about its neighbor
	- **2-way State**: the state when the neighbor returns the hello packet, putting its own router ID down as "My RID" and the original router's as "Neighbor RID". The original router then resends the hello packet, swapping "My RID" and "Neighbor RID" to confirm their relationship.
		- If both routers reach the 2-way state, both routers are ready to become OSPF neighbors
	- **Exstart State**: the state when the master and slave routers are decided (which router will initiate the exchange of information)
		- The router with the higher RID will become the Master and initiate the exchange; the other will be the slave
		- Decision on master and slave is made via Database Description (DBD) packets
	- **Exchange State**: the routers exchange DBDs that contain a list of the LSAs in their LSDB (the DBDs only contain basic info); based on these DBDs, the routers decide what LSAs they need from their neighbor
	- **Loading State**: routers send Link State Request (LSR) messages to request that their neighbor send them any missing LSAs (LSAs sent in Link State Update \[LSU] messages)
		- The routers send LSAck messages to acknowledge that they received the LSAs
	- **Full State**: routers have a full OSPF adjacency and identical LSDBs
		- Continue to send and listen for Hello packets to maintain the neighbor adjacency
		- Every time a Hello packet is received, the "dead" timer (40 seconds by default) is reset; if the dead timer reaches 0 and no hello message is received, the neighbor is removed
		- Routers continue to share LSAs
## Network Types
- Network type = type of connection between OSPF neighbors
- Three main types:
	1. Broadcast - enabled by default on Ethernet and FDDI (Fiber Distributed Data Interfaces) interfaces ^d02102
		- Routers dynamically discover neighbors by sending/listening to OSPF hello messages using multicast address 224.0.0.5
		- A designated router (DR) and backup designated router (BDR) must be elected on each subnet
			- Routers which aren't the DR or BDR become a DROther
			- DR/BDR election:
				1. Highest OSPF interface priority
				2. Highest OSPF router ID ^325f2e
				- "First place" becomes the DR for the subnet, "second place" becomes the BDR
				- The default OSPF interface priority is 1 on all interfaces
				- When the DR goes down, the BDR becomes the DR
				- Routers only form a full OSPF adjacency with the DR and BDR of the segment
					- Thus, routers only exchange LSAs with the DR and BDR; DROthers will not exchange LSAs with each other
					- Reduces the amount of LSAs flooding the network while maintaining the same LSDBs on all routers
					- Send messages to the DR/BDR with multicast address 224.0.0.6
	2. Point-to-point - enabled by default on PPP (point-to-point protocol) and HDLC (high-level data link control) interfaces ^2b6c66
		- Enabled on serial interfaces using the PPP or HDLC encapsulations by default
		- Routers dynamically discover neighbors by sending/listening to OSPF hello messages using multicast address 224.0.0.5
		- A DR and BDR are **not** elected
			- All connections are p2p
		- All routers form full OSPF adjacencies with each other
		- Encapsulation must match on both ends
		- DTE = controller of conversation; DCE = device connected to controller
	3. Non-broadcast - enabled by default on frame relay and X.25 interfaces
## Neighbor/Adjacency Requirements
1. Area number must match for the routers
2. Interfaces must be in the same subnet
3. OSPF process must not be shutdown
4. OSPF router IDs must be unique
5. Hello and Dead timers must match
6. Authentication settings must match (for example, OSPF password)
7. IP MTU settings must match (maximum size of an IP packet) - can become neighbors, but OSPF won't function properly
8. OSPF network type must match - can become neighbors, but OSPF won't function properly
## Link-State Advertisement (LSA) Types
1. Type 1 (Router LSA)
	- Generated by all routers running OSPF
	- IDs the router using its router ID
	- Also lists networks attached to the router's OSPF-activated interfaces
1. Type 2 (Network LSA)
	- Generated by the DR of each "multi-access" network (i.e. the broadcast network type)
	- Lists the routers which are attached to the multi-access network
1. Type 5 (AS External LSA)
	- Generated by ASBRs to describe routes to destinations outside of the AS (OSPF domain)
## CLI
- `(config)# router ospf <process id>` - enters OSPF router config mode
	- Process IDs are locally significant, meaning that routers with different process IDs can still become OSPF neighbors
- `(config-router)# network <IP address> <wildcard mask> area <area number>` - enables OSPF on the interfaces whose IP addresses fall within the given IP address and wildcard mask and configures them for the given area
- `(config-router)# passive-interface <int>` - don't send OSPF hello messages out of the interface, but still send LSAs
	- Always use on an interface with no OSPF neighbors
- `(config-router)# default-information originate` - floods LSAs out of the router
- `R# show ip protocols` - gives info on the router
	- Shows Router ID
		- Router ID order of priority:
			1. Manual configuration
			2. Highest IP address on a loopback interface
			3. Highest IP address on a physical interface
- `(config-router)# router-id <address>` - sets new router ID
	- router must reload to change router ID or use `(config-router)# clear up ospf process` (DON'T USE THIS ON A NETWORK, RESETS ALL OSPF INFO ON THE ROUTER)
- `(config-router)# auto-cost reference-bandwidth <megabits-per-second>` - changes the reference bandwidth to the given value
	- Configure a reference bandwidth greater than the fastest links in your network to allow for future upgrades
	- Configure the same reference bandwidth on all OSPF routers in the network
- `(config-if)# ip ospf cost <int>` - configure the OSPF cost for the interface (takes priority over the autocalculated cost)
- `(config-if)# bandwidth <int>` - changes the bandwidth of the interface (not recommended)
- `(config-if)# speed <int>` - changes the speed of the interface
- `R# show ip ospf interface brief` - displays the OSPF cost of each interface
- `R# show ip ospf neighbor` - displays the OSPF neighbors of the router
- `R# show ip ospf interface <interface>` - shows OSPF information for the designated interface
- `(config-if)# ip ospf <process-id> area <area>` - activates OSPF directly on an interface
- `(config-router)# passive-interface default` - sets all interfaces as OSPF passive interfaces
- `(config-router)# no passive-interface <int>` - sets a passive interface to active
- `(config-if)# ip ospf priority <int>` - changes the OSPF priority of the interface (if priority == 0, the router will never be the DR/BDR)
- `(config-if)# clock rate <int>`- set the speed of a serial interface on the DCE side
- `R# show controllers <interface>` - determine DCE and DTE
- `(config-if)# ip ospf network {broadcast | non-broadcast | point-to-multipoint | point-to-point}` - configure the network type OSPF will use
- `(config-if)# ip ospf {hello-interval | dead-interval}` - set the timer values for the hello- and dead-intervals
- `(config-if)# ip ospf authentication` - enables OSPF authentication
- `(config-if)# ip ospf authentication-key <password>` - sets the OSPF authentication key to the given password
- `(config-if)# ip mtu <int>` - set the MTU on the given interface (MTU = maximum transmission unit; this is the maximum frame size allowed to be received or transmitted on the interface)
