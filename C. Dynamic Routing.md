- Recognizes when a path is no longer available and adjusts its route to a given destination
- Routers can use dynamic routing protocols to advertise info about the routes they know to other routers
	- Form 'adjacencies'/'neighborships' with adjacent routers to exchange this info
	- If multiple routes to a location are found, the superior route is chosen (superiority based on a lower metric)
## Types of Routes:
- Network route: route to a network/subnet (subnet mask length < /32)
- Host route: a route to a specific host (/32 mask)
## Routing Protocol Codes:

| Code | Meaning         |
| ---- | --------------- |
| L    | Local           |
| C    | Connected       |
| S    | Static          |
| R    | RIP             |
| M    | Mobile          |
| B    | BGP             |
| D    | EIGRP           |
| EX   | EIGRP external  |
| O    | OSPF            |
| IA   | OSPF inter area |
| I    | IGRP            |
## Protocols:
### IGP (Interior Gateway Protocol)
- used to share routes within a single autonomous system (AS), which is a single organization (i.e. a company)
- OSPF
- Can be broken down by algorithm type (algorithms determine best routing):
	- Distance Vector
		- Routing Information Protocol (RIP)
		- Enhanced Interior Gateway Routing Protocol (EIGRP)
		- Send their known destination networks and their metric to reach their known destination networks to their neighbors (routing by rumor)
			- Routers only learn the "distance" (metric) and "vector" (direction, the next-hop router) of each route
	- Link State:
		- Open Shortest Path First (OSPF)
		- Intermediate System to Intermediate System (IS-IS)
		- Every router creates a "connectivity map" of the network
		- Each router advertises information about its interfaces (connected networks) to its neighbors
			- The advertisements are passed along to other routers until all have developed the same map of the network
		- Each router uses said map to independently calculate the best routes to each destination
		- More CPU and memory intensive than distance vector, but faster to react to network changes
- Protocol Metrics:
	- Lower = better
		- Think root cost in spanning tree
	- If the router learns two or more routes to the same destination with the same metric, both routes will be added to the routing table and traffic will be load-balanced over both routes
		- Equal Cost Multi-Path (ECMP)
			- Also useable with static routes

#### Metrics

| IGP | Metric | Explanation |
| ---- | ---- | ---- |
| RIP | Hop count | Each router in the path counts as one 'hop'. The total metric is the total number of hops to the destination. Links of all speeds are equal |
| EIGRP | Metric based on bandwidth & delay (by default) | Complex formula that can take into account many values. By default, the bandwidth of the slowest link in the route and the total delay of all links in the route are used |
| OSPF | Cost | The cost of each link is calculated based on bandwidth. The total metric is the total cost of each link in the route (think root cost from STP) |
| IS-IS | Cost | The total metric is the total cost of each link in the route. The cost of each link is not automatically calculated by default. All links have a cost of 10 by default |
### EGP (Exterior Gateway Protocol)
- used to share routes between autonomous systems (ex. routing between companies)
- uses path vector algorithm
- Border Gateway Protocol (BGP) - only modern EGP protocol

### Administrative Distance (AD)
- Metric is used to compare routes learned via the same routing protocol
- AD is used to determine preferred routing protocol (lower AD = better)

| Route protocol/type         | AD  |
| --------------------------- | --- |
| Directly connected          | 0   |
| Static                      | 1   |
| External BGP (eBGP)         | 20  |
| EIGRP                       | 90  |
| IGRP                        | 100 |
| OSPF                        | 110 |
| IS-IS                       | 115 |
| RIP                         | 120 |
| EIGRP (external)            | 170 |
| Internal BGP (iBGP)         | 200 |
| Unusable route (never used) | 255 |
**^ memorize these**

In a routing table, the format is: IP/CIDR \[AD/metric]
You can modify the AD of a routing protocol to prioritize specific routing protocols

### Floating Static Route
- By modifying AD, we can make a static route less preferred than one found via a dynamic routing protocol if the AD for static is higher than that of the routing protocol
- This is called a floating static route
	- The route will be inactive unless the route learned by the dynamic routing protocol is removed (basically acts as a backup)
