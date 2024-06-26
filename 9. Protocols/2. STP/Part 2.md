# STP States/Timers
## Port states
- Blocking & Forwarding = Stable
- Listening & Learning = Transitional
	- Occurs when an interface is activated or when a blocking port transitions to a forwarding state
### Blocking = Non-designated
- Don't send/receive regular network traffic
- Receive STP BPDUs
- Don't forward STP BPDUs
- Don't learn MAC addresses
### Listening = root/designated port going from the blocking state to the listening state
- Only designated or root ports can enter the listening state
- 15 seconds long by default (forward delay timer)
- Only forwards/receives STP BPDUs
	- Doesn't send/receive regular traffic
	- Doesn't learn MAC addresses from regular traffic
### Learning = follows the listening state
- 15 seconds long by default (forward delay timer)
- Only sends/receives STP BPDUs
	- Doesn't send/receive regular traffic
	- DOES learn MAC addresses from regular traffic
### Forwarding = operates as normal
- Sends/receives STP BPDUs
	- Sends/receives normal traffic
	- Learns MAC addresses

| State                               | Send/Receive Regular Traffic? | Learn MAC Addresses? | Forward STP BPDUs? | Receive STP BPDUs? |
| ----------------------------------- | ----------------------------- | -------------------- | ------------------ | ------------------ |
| Forwarding                          | **Yes**                       | **Yes**              | **Yes**            | **Yes**            |
| Learning                            | No                            | **Yes**              | **Yes**            | **Yes**            |
| Listening                           | No                            | No                   | **Yes**            | **Yes**            |
| Blocking (only non-designated port) | No                            | No                   | No                 | **Yes**            |
## Timers
- Hello Timer - How often the root bridge sends hello BPDUs (2 seconds)
	- Hello BPDU only forwarded through designated ports, not root ports
- Forward delay - How long the switch will stay in the listening & learning states (15 seconds)
- Max Age - How long an interface will wait after ceasing to receive Hello BPDUs to change the STP topology (10 * Hello Timer)
- Timers throughout network are determined by route bridge
## STP BPDU
- Regular STP uses dest MAC of `0180.c200.0000`
- Cisco's PVST+ (PVST = ISL, PVST+ = 802.1Q) uses dest MAC of `01:00:0c:cc:cc:cd`
- Message Age = 0 + number of jumps
## STP Port ID
- The format us 0x_ _ _ _
- The first two characters are the port priority
# Optional Features
### Portfast
- Enables a port to go straight to the forwarding state, bypassing listening and learning
	- Must be enabled only on ports connected to end hosts
		- Could create a layer 2 loop if used on a port connected to another switch
	- Enabled with spanning-tree portfast
			Enable on all access ports from global config via spanning-tree portfast default
### BPDU Guard
- When a BPDU is received from another switch, the interface temporarily shuts down to prevent a loop from forming
		Enable with spanning-tree bpduguard enable
			Enable on all portfast-enabled interfaces with spanning-tree portfast bpduguard default
### Root Guard
- Even if the interface receives a superior BPDU, the switch won't accept the new switch as the root bridge and will disable the interface
### Loop Guard
- Even if the interface stops receiving BPDUs, it won't stop forwarding
# Configuration
- Configure the Primary Root Bridge (for each VLAN) on a switch with `(config-if)# spanning-tree vlan <VLAN id> root primary`
	- Sets the STP priority to 24576 or 4096 less than the current lowest priority on a switch
	- STP Load-balancing is the process of configuring a different root bridge for each VLAN
- Configure the cost for a port with `(config-if)# spanning-tree vlan <VLAN id> cost <x>`
- Configure the priority for a port with `(config-if)# spanning-tree vlan <VLAN id> port-priority <x>`