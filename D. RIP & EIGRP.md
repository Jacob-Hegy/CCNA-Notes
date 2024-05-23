## RIP
- industry standard
- distance vector IGP
- Uses hop count as a metric
	- Max count is 15, anything greater than 15 is considered unreachable
- Three versions:
	- RIPv1 & RIPv2, used for IPv4
	- RIPng (RIP Next Generation), used for IPv6
- Two message types:
	- Request: ask RIP-enabled neighbors for their routing tables
	- Response: sending routing table
- Routing tables sent every 30 seconds by default
### RIPv1
- Very old
- Only advertises classful addresses (Class A, B, and C)
	- Doesn't support VLSM or CIDR
	- Doesn't include subnet mask in ads
	- 10.1.1.0/24 becomes 10.0.0.0 (Class A address, so assumed to be /8)
		- VERY BAD
- Messages broadcasted on 255.255.255.255
### RIPv2
- Supports VLSM, CIDR
- Includes subnet mask info in ads
- Messages are multicast to `224.0.0.9`
	- Multicast messages are only delivered to devices in that multicast group
### EIGRP
<small>Know basic concepts, configuration will not be tested</small>
- Enhanced Interior Gateway Routing Protocol
- Considered 'advanced'/'hybrid' distance vector protocol
- Much faster than RIP in reacting to changes in the network
- Doesn't have the 15 hop-count limit
- Sends messages using multicast address `224.0.0.10`
- Only IGP that can perform unequal-cost load-balancing (by default it performs ECMP load-balancing over 4 paths like RIP)
- Metric:
	- Uses bandwidth and delay to calculate metric
		- Simple formula = bandwidth of the slowest link in the path to the destination + the delay of all links to the destination
- Feasible Distance = the router's metric value to the route's destination
- Reported Distance (aka Advertised Distance) = the neighbor's metric value to the route's destination
- Successor = the route with the lowest metric to the destination (the best route)
- Feasible Successor = an alternate route to the destination (not the best route) which meets the feasibility condition
	- Feasibility condition: a route is considered a feasible successor if it's reported distance is lower than the successor route's feasible distance
- Unequal-Cost Load-Balancing:
	- variance x - feasible successor routes with an FD up to x times the successor route's FD can be used to load-balance
# CLI
## RIP
- Enable RIP with `(config)# router rip`
- Use RIPv2 with `(config)# version 2`
- Disable auto-summary (auto-converts to classful addresses) with `(config)# no auto-summary`
- Enable RIP on interfaces that fall within the given range with `(config-router)# network {10.0.0.0 | 172.16.0.0 | 192.168.0.0}`
	- 10.0.0.0 uses /8, 172.16.0.0 uses /12, and 192.168.0.0 uses /16
- Advertise routes on enabled interfaces with `(config-router)# default-information originate`
## EIGRP
<small>Configuration will not be tested</small>
- `(config)# router eigrp 1` - enables EIGRP and sets the autonomous system number (AS); AS must match between routers to form an adjacency and share route info
- `(config)# no auto-summary` - disable auto summarization
- `(config-router)# passive-interface <int>` - disables the advertisement of routes on the given interface
- `(config-router)# network <IP Address> [<Wildcard Mask>]` - you can supply a mask, but if you don't then the IP address is assumed to be classful
	- EIGRP uses a wildcard subnet mask
		- A wild card mask is an inverted subnet mask (all 1s are 0s and all 0s are 1s)
	- A '0' in the wildcard mask means the bits must match in the interfaces IP address and x in the network command
		- '1' in the wildcard mask means the bits don't have to match
- `(config)# eigrp router-id x` - sets the router ID in the AS
	- Router ID order of priority
		1. Manual configuration
		2. Highest IP address on a loopback interface
		3. Highest IP address on a physical interface
## General
- `(config)# network x`
	- Network command looks for interfaces with an IP address in the specified range
	- Activates RIP on interfaces that fall in that range
	- Form adjacencies with connected RIP neighbors
	- Advertise the network prefix of the interface (NOT the prefix in the network command)
	- Perform the network command with the correct classful address for the network interfaces (10.0.0.0, 172.16.0.0, 192.168.0.0)
- `(config)# default-information originate` - advertises its routes to RIP-enabled neighbors
- `R# show ip protocols` - identifies the routing protocol being used as well as its version, maximum path, passive interfaces, active interfaces, and the AD of the interfaces
	- `(config-router)# maximum-paths x` - changes the maximum # of paths to x
	- `(config-router)# distance x` - changes the AD of the current protocol to x
- `R# show ip <protocol> neighbors` - displays neighbors configured by routing protocol
- `#R show ip route {<protocol>|connected}`
- `R# show ip <protocol> topology`
