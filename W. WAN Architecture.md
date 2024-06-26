- When talking about WANs, Star Topology = Hub & Spoke
# Leased Lines
- Dedicated physical link, typically connecting two sites
- Use serial connections (thus layer 2; PPP or HDLC encapsulation)
- A lot of standards (see examples below)

| System       | North American                     | Japanese                                              | European                           |
| ------------ | ---------------------------------- | ----------------------------------------------------- | ---------------------------------- |
| First Level  | 1544 Mbit/s, 24 user channels (T1) | <==                                                   | 2048 Mbit/s, 32 user channels (E1) |
| Second Level | 6312 Mbit/s, 96 channels (T2)      | 6312 Mbit/s, 96 channels OR 7786 Mbit/s, 120 channels | 8448 Mbit/s, 128 channels (E2)     |
| Third Level  | 44736 Mbit/s, 672 channels (T3)    | 32064 Mbit/s, 480 channels                            | 34368 Mbit/s, 512 channels (E3)    |
- Due to higher cost, installation lead time, and slower speeds, Ethernet WAN is becoming more popular
# MPLS VPNs
- Multi Protocol Label Switching
- Service provider networks that are shared infrastructure used by many customer enterprises to make WAN connections over
- **Labels** are used to allow VPNs to be created over the MPLS infrastructure
- Some important terms:
	- **CE router = Customer Edge router; customer router connected to PE router**
	- **PE router = Provider Edge router; provider router connected to CE router**
	- **P router = Provider core router; internal provider router**
![[MPLS Example.png]]

- When PE routers receive frames from the CE routers, they add a label to the frame
	- These labels are used to make forwarding decisions within the service provider network, not the destination IP
- CE routers do not use MPLS, only PE/P
- **When using Layer 3 MPLS VPN, the CE and PE routers peer using OSPF**
- In a Layer 2 MPLS VPN, the CE and PE routers do not form peerings
	- In this case, the service provider network is entirely transparent to the CE routers
	- **It's effectively like the two CE routers are directly connected (their WAN interfaces will be in the same subnet)**
	- If a routing protocol is used, the two CE routers will peer directly with each other
	- **Basically imagine the service provider's network as a massive switch**
- Different kinds of connections can be used by different clients to connect to the same service provider (Ethernet, 4G/5G, Cable, Serial, etc.)
# Internet Connectivity
### Digital Subscriber Line (DSL)
- Provides Internet connectivity to customers over phone lines
- A DSL modem is required to convert data into a format suitable to be sent over phone lines
### Cable Internet
- Provides Internet connectivity to customers over Cable Television (CATV) lines
- Also needs a modem
### Redundancy
- 1 connection to 1 ISP = **Single Homed**
	- Unideal for enterprise, fine for single user/home
	![[Single Homed.png]]
- 2 connections to 1 ISP = **Dual Homed**
	- Better, but still not ideal for enterprise
	![[Dual Homed.png]]
- 1 connection each to 2 ISPs (2 total connections) = **Multihomed**
	- Even better
	![[Multihomed.png]]
- 2 connections each to 2 ISPs (4 total connections) = **Dual Multihomed**
	- Best redundancy, but potentially unnecessary
	![[Dual Multihomed.png]]
# Internet VPNs
- Provide secure communications between sites over the Internet
- Two kinds:
	1. **Site-to-site with IPsec VPNs**
		- Site-to-site = VPN between to devices to connect two sites together over the Internet
		- VPN tunnel is created by encapsulating the original IP packet with a VPN header and a new IP header
			- When using IPsec, the original packet is encrypted before being encapsulated with the new header
	![[IPsec VPN.png]]
		- Doesn't support broadcast and multicast traffic, only unicast
			- This means that routing protocols like OSPF can't be used over tunnels
				- This can be solved with GRE over IPsec
		- Configuring a full mesh of tunnels between many sites is labor intensive
			- Can be solved with Cisco's DMVPN
			#### GRE over IPsec
			- GRE = Generic Routing Encapsulation
			- Creates tunnels like IPsec, but does NOT encrypt the original packet
			- Capable of encapsulating a wide variety of Layer 3 protocols + broadcast & multicast messages
			- Combining GRE and IPsec allows the flexibility of the former with the security of the latter
			- The original packet is encapsulated with a GRE header and a new IP header, then that encapsulated packet is encrypted and encapsulated with an IPsec VPN header and new IP header
				Encryption (Original IP Packet + GRE Header + New IP Header) + IPsec VPN Header + *New* New IP Header
			#### DMVPN
			- DMVPN = Dynamic Multipoint VPN
			- Allows routers to dynamically create a full mesh of IPsec tunnels without having to manually configure every single tunnel
			- Step 1: Configure IPsec tunnels to a hub site
			- Step 2: Hub router gives each router info on how to form an IPsec tunnel with the other routers
			![[DMVPN Configuration.png]] ^634cea
	1. **Remote access VPNs with TLS** 
		- Allows end devices to access a company's internal resources securely over the Internet
		- Uses TLS to form secure tunnels to one of the company's routers/firewalls which is acting as a TLS server
		![[DMVPN Diagram.png]]
