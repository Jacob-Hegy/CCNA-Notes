# Purpose
- Redundancy in networks
	- Helps to meet constant uptime
	- If a component fails, other components should pick up the slack with little or no downtime
	- Implement at every possible point in the network
	- Bad design: single router, single connections to switches, etc.
	- Good design: multiple routers, multiple ways to reach a switch
	- Servers often have multiple NICs, so plugging into multiple switches can provide redundancy
		- PCs usually don't have the capability to plug into multiple switches
	- STP enables redundant layer 2 networks
- Broadcast Storms
	- Occurs when a switch doesn't know where a broadcast came from, resulting in a continuous, infinite broadcast
		- Layer 2 lacks a TTL
		- Congests network and forces switches to continuously update their MAC address table (MAC flapping)
# Spanning Tree Protocol (STP)
- On all switches by default
- Places redundant ports in a blocking state (basically disables the interface)
	- Will enter a forwarding state if an active, currently forwarding interfaces fails
- Interfaces in a forwarding state behave normally, sending and receiving all normal traffic
- Blocking interfaces only send and receive BPDU and STP traffic
	- ***When spanning tree refers to bridge, it means switch***
- Prevents broadcast storms
- By using blocking ports, a single path to and from each point in the network is created to prevent loops
### Process of selecting blocking and open ports
1. All switches send out Hello BPDU packets
	- Any switch that receives a Hello BPDU knows that it is connected to another switch
	- Any port that does not receive a Hello BPDU stays open
2. A root bridge is determined ^4a8be5
	- The root bridge is the one with the lowest bridge ID
		- Bridge ID = `Bridge Priority (16 bits) | MAC Address (48 bits)`
			- Bridge Priority: Bridge Priority (4 bits) | Extended System ID (VLAN ID) (12 bits)
			- Given that Bridge Priority is 16 bits and the most significant bit of Bridge Priority is default 1, the bridge priority is actually 32768 + VLAN ID
			- Bridge Priority can only be incremented in amount of 4096
	- By default, all bridges have a default bridge priority of 32768
		- In the event of a tie, the lowest MAC address is designated the root bridge
3. Set all ports on the root bridge in a forwarding state
	- All other switches must have a route to the root bridge
- When a switch is powered on, it assumes it's the root bridge until met with a BPDU with a lower bridge ID (a "superior" BPDU)
	- Once the root bridge has been decided on, only the root bridge sends BPDU; other bridges forward BPDUs from the root bridge
### Process after designating root bridge
- All other switches will select one interface to be its root port; this port will be in a forwarding state
- Root port is determined based on the port with the lowest root cost ^f0be2c
- [[#Root Cost Table|Root cost]] = total cost of outgoing interfaces on the path to the root bridge
	- The root cost of all ports on the root bridge is 0
- All bridges send out BPDU packets advertising their root cost
- Bridges receive these BPDUs and send them out of their outgoing interfaces while incrementing them by the cost of the interface
	- From there, they choose their root port
	- The root port is the bridge's path to the root bridge
	- In the event of a tie, the root port is the one connecting to the port with the lowest neighbor bridge ID
	- If both root cost and neighbor bridge ID tie, then lowest neighbor port ID is used as the final tiebreaker
		- STP Port ID = port priority (default 128) + port number
			- G0/0 lower than G0/1 lower than G1/0 etc.
### After designating all root ports and designated ports...
- Remaining connections must select which interface will be the designated port
- Lowest root cost port will become designated
	- If same root cost, use lowest bridge ID
- The non-designated port will be non-designated (blocking)
#### Root Cost Table
| Port Speed | Cost | Port Name          |
| ---------- | ---- | ------------------ |
| 10 Mbps    | 100  | Ethernet           |
| 100 Mbps   | 19   | Fast Ethernet (Fa) |
| 1 Gbps     | 4    | Gigabit (G)        |
| 10 Gbps    | 2    | 10 Gigabit         
# Configuration
- View port costs and roles (root/designated/non-designated) with `R# show spanning-tree [vlan x]`
	- View more info (such as total cost for a port) with `# show spanning-tree detail`
	- View the amount of ports of each type running in each VLAN with `# show spanning-tree summary`
