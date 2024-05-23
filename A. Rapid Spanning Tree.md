## Spanning Tree Versions
### Classic STP (802.1D)

- Original STP
- All VLANs share one STP instance
	- This means no load balancing
### Per-VLAN Spanning Tree Plus (PVST+)
- Cisco's upgrade to 802.1D
- Each VLAN has its own STP instance
	- Can load balance by blocking different ports in each VLAN
### Rapid Spanning Tree Protocol (802.1w)
- Much faster at reacting to network changes than 802.1D
- All VLANs share one STP
	- Still no load balancing
### Rapid Per-VLAN Spanning Tree Plus (Rapid PVST+)
- Cisco's upgrade to 802.1w
- Each VLAN has its own STP instance
	- Allows load balancing
### Multiple Spanning Tree Protocol (802.1s)
- Uses modified RSTP mechanics
- Can group multiple VLANs into different instances (ex. VLANs 1-5 in instance 1
- VLANs 6-10 in instance 2) to perform load balancing
Cisco devices run 802.1s
Best to use MSTP for large networks
For smaller networks, use Rapid PVST+
## Rapid PVST+
Uses a bridge-to-bridge handshake mechanism
Purpose is to block ports to prevent Layer 2 loops
Selects a [[Part 1#^4a8be5|root bridge]], [[Part 1#^f0be2c|root ports]], and [[Part 1#Process after designating root bridge|designated ports]] with the same rules as STP
### Rapid Spanning Tree Port States
| STP Port State | Send/Receive BPDUs | Frame forwarding (regular traffic) | MAC address learning | Stable/Transitional |
| :------------: | :----------------: | :--------------------------------: | :------------------: | :-----------------: |
|   Forwarding   |      YES/YES       |                YES                 |         YES          |       Stable        |
|   Discarding   |       NO/YES       |                 NO                 |          NO          |       Stable        |
|    Learning    |      YES/YES       |                 NO                 |         YES          |    Transitional     |
### Port Roles
Root and designated port roles remain unchanged
Non-designated port divided into two separate roles
- Alternate port role
	- A discarding port that receives a superior BPDU from another switch (same as a blocking port)
	- Functions as a backup to the root port
		- Should the root port fail, the backup port immediately picks up for it and becomes forwarding (no transitional states)
- Backup port role
	- A discarding port that receives a superior BPDU from another interface on the same switch
		- Only happens when two interfaces are connected to the same collision domain (via a hub)
			- Hubs aren't used in modern networks, so you'll likely never see an RSTP backup port
	- Functions as a backup for a designated port
	- Interface with the lowest port ID will be the designated port, and the other will be the backup port
Rapid STP is compatible with Classic STP
	An RSTP switch connected to a classic STP switch will operate in classic STP mode
### BPDU
Protocol Version Identifier: Rapid Spanning Tree (2)
BPDU Type: Rapid/Multiple Spanning Tree (0x02)
BPDU Flags are used to negotiate bridge-to-bridge connections faster than the transitioning from blocking to forwarding
**In Rapid STP, ALL switches originate and send their own BPDUs from their designated ports**

### Functionality
- All switches running RSTP send their own BPDUs every hello time (2 seconds)
- A switch considers its neighbor lost if it misses 3 BPDUs (6 seconds); if designated as lost, all MAC addresses on that interface are flushed

### Link Types
- Edge: a port that is connected to an end host; moves directly to forwarding without negotiation
	- Switch --> PC/Server
	- Enabled with ```spanning-tree portfast```
- Point-to-point: a direct connection between two switches
	- Full-duplex mode
	- No configuration necessary
		- If you really want to, use ```spanning-tree link-type point-to-point```
- Shared: a connection to a hub
	- Half-duplex mode
	- No configuration necessary
		- If you really want to, use ```spanning-tree link-type shared```
	- Very unlikely to see because hubs aren't used in modern networks

### Port Costs

| Bandwidth | Cost    |
| --------- | ------- |
| 10 Mbps   | 2000000 |
| 100 Mbps  | 200000  |
| 1 Gbps    | 20000   |
| 10 Gbps   | 2000    |
| 100 Gbps  | 200     |
| 1 Tbps    | 20      |
| 10 Tbps   | 2       |
# Configuration
- Select Spanning Tree Mode with ```(config)# spanning-tree mode [mst/pvst/rapid-pvst]```
- Check the Spanning Tree Config with ```S# show spanning-tree```
