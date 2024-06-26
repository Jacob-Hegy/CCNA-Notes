![[SDN Architecture Diagram.png]]
# Cisco SD-Access
- Cisco's SDN solution for automating campus LANs
	- ACI (Application Centric Infrastructure) is used for data center
	- SD-WAN is used for automating WANs
- Cisco DNA (Digital Network Architecture) Center is the controller at the center of SD-Access

![[Cisco DNA Diagram.png]]

### Fabric
#### Underlay
- The underlying physical network of devices and connections which provide IP connectivity
	- Basically a bunch of multilayer switches and their connections
- Purpose is to support the VXLAN tunnels of the overlay
- Three different roles for switches in SD-Access:
	1. Edge nodes: connect to end hosts
	2. Border nodes: connect to devices outside of the SD-Access domain (ex. WAN routers)
	3. Control nodes: use LISP (Locator ID Separation Protocol) to perform various control plane functions
- SD-Access can be added on top of an existing network as long as the hardware and software support it (this is known as a **brownfield deployment**)
- A new deployment done completely from scratch will be configured by DNA Center to use the optimal SD-Access underlay (this is called **greenfield deployment**)
	- All switches are Layer 3 and use IS-IS as their routing protocol
	- All links between switches are routed ports, thus no need for STP
	- Edge nodes (access switches) act as the default gateway of end hosts (*routed access layer*)

![[Underlay Diagram.png]]

![[SD-Access Underlay Diagram.png]]

#### Overlay
- The virtual network built on top of the physical underlay network
	- SD-Access uses VXLAN (Virtual Extensible LAN) to build tunnels)
- LISP provides the control plane of SD-Access
	- A list of mappings of EIDs (endpoint identifiers) to RLOCs (routing locator) is kept
		- EIDs identify end hosts connected to edge switches
		- RLOCs identify the edge switch which can be used to reach the end host
- **Cisco TrustSec (CTS)** provides policy control (QoS, security policy, etc.)
- VXLAN provides the data plane of SD-Access

![[Overlay Diagram.png]]

![[SD-Access Overlay Diagram.png]]

- Fabric = the combination of the overlay and underlay
![[Fabric Diagram.png]]
# Cisco DNA Center
- Two main roles:
	1. The SDN controller in SD-Access
	2. A network manager in a traditional network (non-SD-Access)
- Note: DNA Center is a software app installed on Cisco UCS server hardware
- Has a REST API used to interact with DNA Center
- The SBI supports protocols such as NETCONF and RESTCONF (as well as traditional protocols like Telnet, SSH, SNMP, etc.)
- Enables **Intent-Based Networking (IBN)**
	- Allows the engineer to communicate their intent for network behavior to DNA Center, and then DNA Center will take care of the details of the actual configurations and policies on devices
#### Practice DNA Center:

![[DNA Center Practice Info.png]]
# DNA Center Network Management vs Traditional
### Traditional Network Management
- Devices are configured one-by-one via SSH or console connection
- Manual configuration before deployment
- Configurations and policies are managed per-device (distributed)
- New network deployments can take a long time due to the manual labor required
- Errors and failures are more likely due to increased manual effort
### DNA Center-based Network Management
- Devices are centrally managed and monitored from the DNA Center GUI or other applications using its REST API
- The administrator communicates their intended network behavior to DNA Center, which changes those intentions into configurations on the managed network devices
- Configurations and policies are centrally managed
- Software versions are centrally managed and cloud servers can be monitored for new versions
- New network deployments are much quicker
