# Benefits
- Reduce human error during configuration
- Network scalability increased because deployments, changes, and troubleshooting can be performed at scale much faster
- Network-wide policy compliance can be assured
- Reduced operating expenses (opex) to maintain the network
# Logical Planes of Network Functions
- The various functions of network devices can be logically divided up (categorized) into planes
### Data/Forwarding Plane
- All tasks involved in forwarding user data/traffic from one interface to another
	- Ex. Routers, Switches, NAT, etc.
- A router receives a message, looks for the most specific matching route in its routing table, and forwards it out of the appropriate interface to the next hop
	- It also de-encapsulates the Layer 2 (MAC) header and re-encapsulates the frame with a new header for the next hop's MAC address
- A switch receives a message, looks at the destination MAC address, and forwards it out of the appropriate interface (or floods it if it's an unrecognized MAC address)
	- Also adds and removes 802.1q VLAN tags
- NAT changes the source/destination addresses before forwarding
- Decisions made on forwarding or discarding messages according to ACLs, port security, etc.
### Control Plane
- Functions that construct the functionality of the data plane
	- Ex. Building a router's routing table to determine the flow of traffic
	- Ex. Routing table, MAC address table, ARP table, STP, OSPF, etc.
- Performs overhead work 
	- OSPF doesn't forward user data packets itself, instead it informs that data plane about how to forward the packets
	- STP doesn't directly forward packets, but it informs the data frame about which interfaces should or shouldn't be used to forward frames
	- ARP messages aren't user data, but they're used to build an ARP table which is used to process and forward data
- Each device has its own Data plane and Control plane
### Management Plane
- Performs overhead work that consists of protocols that are used to manage devices
	- SNMP, SSH, Telnet, Syslog, NTP, etc.

![[Logical Planes Diagram.png]]

- The Management and Control planes are usually managed by the CPU, but the Data plane requires faster processing
	- Thus, specialized hardware ASICs (Application-Specific Integrated Circuits) are used
- The MAC address table is stored in **TCAM (Ternary Content-Addressable Memory)**
	- The MAC address table is also called the **CAM table**
# SDN
- An approach to networking that centralizes the control plane into an application called a controller
	- Also called **Software-Defined Architecture (SDA)** or **Controller-Based Networking**
- Traditional control planes use a distributed architecture
	- Ex. Each router in the network runs OSPF and the routers share routing info amongst themselves to calculate their preferred routes to each destination
- An SDN controller centralizes control plane functions like calculating routes
- The controller can interact with the network devices using APIs

![[SDN Controller Example.png]]
<small>The controller communicates with the routers via its Southbound Interface (SBI)</small>
### Southbound Interface (SBI)
- Used for communications between the controller and the network devices it controls
	- Typically consists of a communication protocol and API
- Virtual interface
- Examples: OpenFlow, Cisco OpFlex, Cisco onePK (Open Network Environment Platform Kit), NETCONF
### Northbound Interface (NBI)
- The SBI communicates with the managed devices to gather configure them and gather info such as:
	- The devices in the network
	- The topology
	- The available interfaces on each device
	- Their configurations
	- Etc.
- The Northbound Interface (NBI) allows us to interact with the controller, access the data it gathers, program it, and makes changes in the network via the SBI

![[NBI Diagram.png]]

- A [[43. REST APIs|REST API]] is used on the controller as an interface for apps to interact with it
	- REST = Representational State Transfer
- The API will send a GET message to request data from the Controller, and the Controller will respond with data in a specific structure such as a JSON or XML
### Automation in Traditional Networks vs SDN
- Networking tasks can be automated in traditional networks with stuff like Python scripts
- SDN controllers facilitate this automation naturally because of the centralized collection of data and the format which it's stored in (JSON, XML, etc.)
	- Also supports network-wide analytics
	- SDN tools allow non-technical users to perform automation tasks as well
	- APIs can also interact with 3rd party apps
