## Basics
- 128 bit address
	- Written as 8 groups of 4 hexadecimal characters each (4 bits per hex character)
- Uses slash notation to indicate subnet mask
	- Ex. 2001:0DB8:5917:EABD:6562:17EA:C92D:59BD/64
		This means the first 64 bits of this address are for the subnet mask
- Leading zeros can be removed
	- Ex. 2001:0DB8:000A:001B:20A1:0020:0080:34BD
		Reduces down to 2001:DB8:A:1:20A1:20:80:34BD
- Consecutive quartets of all 0s can be replaced with a double colon (::)
	- Ex. 2001:0DB8:0000:0000:0000:0000:0080:34BD
		Reduces down to 2001:0DB8::0080:34BD
		Further reduces to 2001:DB8::80:34BD
	- **NOTE:** Consecutive quartets can only be abbreviated once
		- Ex. 2001:0000:0000:0000:20A1:0000:0000:34BD
			This cannot become 2001::20A1::34BD because we have no idea how many quartets each double colon represents
			
			Instead, reduce to 2001::20A1:0:0:34BD
- Finding the IPv6 prefix (global unicast addresses)
	- Find where the network portion of the address ends and make all the following numbers 0
## Configuring IPv6
| CLI Command                       | Result                                                |
| --------------------------------- | ----------------------------------------------------- |
| (config-if)# ipv6 enable          | Enables IPv6 on the interface                         |
| (config)# ipv6 unicast-routing    | Enables IPv6 routing on a router                      |
| (config-if)# ipv6 address X/Y     | Configures the given address X with the subnet mask Y |
| (config-if)# do sh ipv6 int brief | Shows IPv6 addresses of interfaces                    |
### Modified EUI-64 (Extended Unique Identifier)
- Method of converting MAC address into a 64-bit interface ID
- This interface ID can then become the "host portion" of a /64 IPv6 address
- How to convert the MAC address:
	1. Divide the MAC address in half
		1234 5678 90AB ==> 1234 56 | 78 90AB
	2. Insert FFFE in the middle
		1234 56**FF** **FE**78 90AB
	3. Invert the 7th bit
		1**0**34 56FF FE78 90AB
		Converted the 1 in 0010 to a 0
- Configuring EUI-64 on an interface:
	(config-if)# ipv6 address *network prefix of IPv6 address* eui-64
		ex. ipv6 address 2001:db8::/64 eui-64
## Address Types
**NOTE: In the following subtopics, the examples all have a subnet mask of /64. This is not required, but it is the most common**
### Global Unicast Addresses
- Public IPv6 addresses which can be used over the internet
	- Must register to use them
- Includes all addresses not already reserved for other purposes

Example with address 2001:0DB8:8B00:0001:XXXX:XXXX:XXXX:XXXX/64

| Part of Global Unicast Address | Purpose                                                                                        |
| ------------------------------ | ---------------------------------------------------------------------------------------------- |
| 2001:0DB8:8B00                 | 48-bit global routing prefix assigned by the ISP                                               |
| 0001                           | 16-bit subnet identifier used by the enterprise to make various subnets                        |
| XXXX:XXXX:XXXX:XXXX            | 64-bit "interface identifier", the host portion of the address (can be calculated with EUI-64) |
### Unique Local Addresses
- Private IPv6 addresses that cannot be used over the network
	- No need to register; used within internal networks
- Use the address block FD00::/7 (thus the range is FD00:: to FDFF:FFFF:FFFF:FFFF:FFFF:FFFF:FFFF:FFFF)

Example with address FD45:93AC:8A8F:0001:XXXX:XXXX:XXXX:XXXX/64

| Part of Unique Local Address | Purpose                                                                                                          |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| FD                           | Indicates a unique local address                                                                                 |
| 45:93AC:8A8F                 | 40-bit global ID, which should be randomly generated (prevents collision errors in the event of a network merge) |
| 0001                         | 16-bit subnet identifier used by the enterprise to make various subnets                                          |
| XXXX:XXXX:XXXX:XXXX          | 64-bit "interface identifier", the host portion of the address (can be calculated with EUI-64)                   |
### Link Local Addresses
- IPv6 addresses automatically generated on IPv6-enabled interfaces
- Use the address block FE80::/10
	- However, the 54 bits after FE80/10 should all be 0, so it will only ever start with FE8
- Automatically configured with `ipv6 enable`
- Interface ID is generated with EUI-64 rules
- These addresses are used for communicating within a single subnet; packets will not be routed to destinations with these IPv6 addresses
- Common uses:
	- Routing protocol peering (OSPFv3 uses link-local addresses for neighbor adjacencies)
	- Next-hop addresses for static routes
	- Neighbor Discovery Protocol (NDP; IPv6's replacement for ARP)

Example:
![[Link-Local Address Example.png]]
Note that the routers within the pink box only have link local addresses. They cannot ping each other, but next hop protocols are totally fine, allowing for communication between PC1 and PC2
### Multicast Addresses
- Uses address block FF00::/8 for multicast
- IPv6 does not use broadcast (but there is a multicast address that functions like broadcast)

| Purpose                                    | IPv6 Address | IPv4 Address |
| ------------------------------------------ | ------------ | ------------ |
| All nodes/hosts (functions like broadcast) | FF02::1      | 224.0.0.1    |
| All routers                                | FF02::2      | 224.0.0.2    |
| All OSPF routers                           | FF02::5      | 224.0.0.5    |
| All OSPF DRs/BDRs                          | FF02:6       | 224.0.0.6    |
| All RIP routers                            | FF02::9      | 224.0.0.9    |
| All EIGRP routers                          | FF02::A      | 224.0.0.10   |
- IPv6 defines multiple multicast "scopes" which indicate how far the packet should be forwarded
	- All addresses in the table above use the "link-local" scope (FF02), which stays in the local subnet
- Scopes:
	- Interface-local (FF01): packets don't leave the local device
	- Link-local (FF02): packets don't leave the local subnet
	- Site-local (FF02): packets can be forwarded by routers, but they should be limited to a single physical location (don't forward over a WAN; up to the network engineer to configure)
	- Organization-local (FF08): wider in scope than site-local (ex. entire company)
	- Global (FF0E): no boundaries; can be routed over the internet
	![[Scopes Diagram.png]]
	- Check what multicast groups an interface is a part of with `sh ipv6 int *interface*`
### Anycast Addresses
- New feature of IPv6
- "One-to-one-of-many" ==> if unicast is sending to one individual, broadcast is sending to everyone, and multicast is sending to everyone in a group, anycast is sending to an individual in a group
- Multiple routers are configured with the same IPv6 address
	- They use a routing protocol to advertise the address
	- When hosts send packets to that destination address, routers forward it to the nearest router configured with that IP address (based on routing metric)
- No specific address range

Example:
`(config-if)# ipv6 address 2001:db8:1:1::99/128 anycast`
- The host route to this address will now be advertised to other routers
### Other IPv6 Addresses
- :: = unspecified IPv6 address
	- Can be used when a device doesn't know its IPv6 address yet
	- IPv6 default routes are configured to ::/0
	- IPv4 equivalent: 0.0.0.0
- ::1 = the loopback address
### Solicited-Node Multicast Address
- Calculated from a unicast address
	ffe0:0000:0000:0000:0000:0001:ff + last 6 hex digits of unicast address
	ex. *above* + a3:00b1 = ff02::1:ffa3:b1
### Neighbor Discovery Protocol (NDP)
- Replaces ARP for IPv6
- Uses ICMPv6 and Solicited-Node multicast addresses to learn the MAC addresses of other hosts
- Two messages types:
	- Neighbor Solicitation (NS) = ICMPv6 Type 135
	- Neighbor Advertisement (NA) = ICMPv6 Type 136
- Also allows hosts to automatically discover routers on the local network
	- Router Solicitation (RS) = ICMPv6 Type 133
		- Sent to multicast address FF02::2 (all routers)
	- Router Advertisement (RA) = ICMPv6 Type 134
		- Sent BY router to multicast address FF02::1 (all nodes)
		- Sent in response to RS & sent periodically
### SLAAC
- Stands for Stateless Address Auto-configuration
- Hosts use the RS/RA messages to learn the IPv6 prefix of the local link (i.e. 2001:db8::/64) and then automatically generate an IPv6 address
- Command: `ipv6 address autoconfig`
### Duplicate Address Detection (DAD)
- DAD allows hosts to ensure no other devices on the local link are using the same IPv6 address
- Performed anytime IPv6 is configured or an IPv6-enabled interface initializes (`no shutdown` command)
- Uses NS and NA
	- Sends an NS to its own IPv6 address; if it gets a reply, some other endpoint has its address as well. If not, it knows its address is unique
### IPv6 static routing
- Works the same as IPv4 routing
	- Has separate routing table though
- Enabled with `ipv6 unicast-routing`
- Even if IPv6 routing is disabled, the router can still send and receive IPv6 traffic it just won't route it/forward it between networks
- Routes are automatically added for addresses configured on the router
- Configure a floating static route by configuring an additional static route to a destination with a higher administrative distance than the currently enabled route
- Route Examples:
### Network Route
`ipv6 route 2001:db8:0:3::/64 2001:db8:0:12::2`
### Host Route
`ipv6 route 2001:db8:0:1::100/128 2001:db8:0:12::1`
`ipv6 route 2001:db8:0:3::100/128 2001:db8:0:23::2`
### Default Route
`ipv6 route ::/0 2001:db8:0:23::1`
- To configure a route as a backup route, change the administrative distance to one greater than 1
