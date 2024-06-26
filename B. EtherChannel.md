Oversubscription = bandwidth of end host interfaces > bandwidth of connection to the distribution switch(es)
	A distribution switch is the switch that directs the traffic in the network

- EtherChannel - groups multiple interfaces together to act as a single interface (circumvents the issue of STP blocking traffic from all but one of the interfaces)
	- This prevents broadcast storms as a broadcast frame is only sent once over the grouped interfaces
	- Other names: Port Channel, LAG (Link Aggregation Group)
	- STP treats the EtherChannel as a single interface
## Load Balancing:
- Based on "flows" (a communication between two nodes in the network)
- Frames in the same flow use the same physical interface
- Metrics available to use for interface selection:
	- Source/Destination MAC Address (either one or even both)
	- Source/Destination IP Address (either one or even both)

## Methods for EtherChannel Configuration:
### PAgP (Port Aggregation Protocol)
- Cisco proprietary
- Dynamically negotiates the creation/maintenance of the EtherChannel (like DTP does for trunks)
### LACP (Link Aggregation Control Protocol)
- IEEE 802.3ad (industry standard)
- Dynamically negotiates the creation/maintenance of the EtherChannel (like DTP does for trunks)
### Static EtherChannel
- No protocol
- Interfaces statically configured
- Usually avoided

Up to 8 interfaces can be formed into a single EtherChannel (LACP allows up to 16, but 8 will be active and 8 will be on standby in case one of the active interfaces fail)

Once the EtherChannel is configured, it essentially works like an normal interface

Both channels in an EtherChannel connection must have matching configs:
- Same duplex
- Same speed
- Same switchport mode (access/trunk)
	- Same allowed VLANs/native VLAN (for trunk interfaces)

Layer 2 EtherChannels are usually configured on switches
Layer 3 EtherChannels are usually configured on multilayer switches
	Layer 3 EtherChannels circumvent STP altogether because layer 2 broadcasts are not forwarded on layer 3

# Configuration
- Check calculations for determining physical interface to use with `S# show etherchannel load-balance`
- Configure the measurements used for load balancing with `(config)# port-channel load-balance {src-mac | dst-mac | src-dst-mac | src-ip | dst-ip | src-dst-ip | src-port | dst-port | src-dst-port | mpls}`
- Configure interfaces into an EtherChannel with `(config-if-range)# channel-group <1-64> mode {active | passive} | {auto | desirable} | {on}`
	- PAgP uses auto/desirable (two autos don't form a connection, auto/desirable and desirable/desirable do)
	- LACP uses active/passive (same idea as PAgP)
	- Static uses on (on only works with on mode, not desirable or active)
- `channel-protocol {pgap | lacp}`
	- Use either lacp or pgap
- Show the configured EtherChannels with info about them with `R# show etherchannel summary`
	- Flags:
		- `S` = Layer 2
		- `R` = Layer 3
		- `s` = suspended
		- `D` = down
		- `U` = in use
		- `P` = interface bundled in port-channel
- View number of ports, protocol, and more with `R# show etherchannel port-channel`
- When configuring a Layer 3 EtherChannel use `(config-if)# no switchport`
	- After configuring with channel-group, give an IP address
