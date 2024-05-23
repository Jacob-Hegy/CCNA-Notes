# Overview
- Used to divide a router into multiple virtual routers
	- Similar to dividing a LAN in VLANs
- Accomplished by allowing the router to build multiple routing tables
	- Interfaces (Layer 3 only) & routes are configured to be in a specific VRF
	- Router interfaces, [[3. Multilayer Switches (Layer 3 Switches)#Switch Virtual Interfaces (SVIs)|SVIs]], and routed ports on [[3. Multilayer Switches (Layer 3 Switches)|multilayer switches]] can be configured in a VRF
- Traffic in one VRF **cannot** be forwarded out of an interface in another VRF
	- *VRF Leaking* can allow this, but that's beyond the scope of the CCNA
- Used to facilitate [[33. WAN Architecture#MPLS VPNs|MPLS]]
	- This allows service providers to allow one device to carry traffic from multiple customers
		- Each customer's traffic is isolated from one another
		- Customer IP address can overlap without any issues
# Configuration
<small>Not on the CCNA</small>
Configuration will follow this example:
![[VRF Configuration Diagram.png]]

- Create the VRF with `(config)# ip vrf <VRF name>`
- View the existing VRFs with `R# show ip vrf`
- Assign an interface to an existing VRF with `(config-if)# ip vrf forwarding <VRF name>`
	- The interface will be disabled and its IP address will be removed when assigned to a VRF
- View the IP routes for a VRF with `R# show ip route vrf <VRF name>`
- View the global routing table with `R# show ip route`
- To ping via a VRF use `R# ping <VRF name> <IP address>`
