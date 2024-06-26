# Basics
- DHCP servers = UDP 67; DHCP clients = UDP 68
## DHCP DORA
1. DHCP Discover: Client --> Broadcast Address (255.255.255.255) "are there any DHCP servers? I need an IP address"
2. DHCP Offer: Server --> Client  (can be broadcast or unicast) "Hi, how about this IP address?"
3. DHCP Request: Client --> Broadcast Address (255.255.255.255) "yes please, I want it"
4. DHCP Ack: Server --> Client (can be broadcast or unicast) "okay sure, you may use it"
- DHCP Release: Client --> Server (unicast) "okay I'm done, take it back"
## DHCP Relay
- Individuals routers can be configured as DHCP servers OR a single centralized server can be made and the routers setup as DHCP relay agents
	- This means that any DHCP broadcasts that the routers receive will be forwarded as unicast to the DHCP server
# Configuring DHCP
- Specify a range of addresses to not give to DHCP clients with `(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10`
- Create a DHCP pool with `(config)# ip dhcp pool LAB_POOL`
- Specify the subnet of addresses to be assigned to clients with `(dhcp-config)# network <IP_ADDRESS> /CIDR`
- Specify the DNS server for the clients in the pool with `(dhcp-config)# dns-server <IP_ADDRESS>`
- Specify the domain name of the network with `(dhcp-config)# domain-name <domain>`
- Specify the default gateway with `(dhcp-config)# default-router <IP_ADDRESS>`
- Specify the lease time with `(dhcp-config)# lease {<days> <hours> <minutes> | infinite}`
- View DHCP info for a given device with `R# show ip dhcp binding` or `ipconfig /all` for Windows machines
- Configure the IP address of the DHCP server as the helper address on a router's interface with `(config-if)# ip helper-address <IP_ADDRESS>`
- Set a router to use DHCP to learn its IP address with `(config-if)# ip address dhcp` ^54f7e0
