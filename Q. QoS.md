# IP Phones/Voice VLANs
- Uses VoIP to enable telecommunication over the internet
	- Connects to a switch
- Have an internal 3-port switch
	- 1 port for the uplink (external switch), 1 port for the downlink (the PC), and 1 port for the phone itself
- Generally recommended to separate voice traffic from IP phones and data traffic from the PC into separate VLANs
	- Configure a voice VLAN so PC traffic is untagged and voice traffic is tagged for the appropriate VLAN
	![[VoIP VLAN Config.png]]
# Power over Ethernet (PoE)
- Allows electricity to be received through an ethernet cable
- PSE (Power Sourcing Equipment) provides power to PDs (Powered Devices)
	- Typically the PSE is a switch
- PSE receives AC power from the outlet, converts it to DC power, and supplies that DC power to the PDs
- Power policing is used to prevent a PD from taking too much power
	- `(config-if)# power inline police` configures power policing; it will disable the port and send a Syslog message if a PD draws too much power
	- `(config-if)# power inline police action log` doesn't shut down the interface, instead restarting it and sending the Syslog message
# Quality of Service (QoS)
- Manages four characteristics of network traffic:
	1. Bandwidth: allows you to reserve a certain amount of bandwidth for specific kinds of traffic
	2. Delay:
		- One-way delay = the amount of time it takes to go from source to destination
		- Two-way delay = the amount of time it takes to go from source to destination and back again
	3. Jitter: the variation in one-way delay between packets sent by the same application
	4. Loss: % of packets sent that don't reach their destination
- Recommended standards of quality for acceptable interactive audio:
	- One-way delay: <= 150ms
	- Jitter <= 30ms
	- Loss: <= 1%
- Queuing:
	- If messages are received faster than they can be forwarded, the messages are placed in a queue
	- If the queue becomes filled up, new packets are dropped (tail drop)
		- This can lead to TCP global synchronization
			- Remember TCP sliding window, which means while a TCP connection is able to successfully send TCP packets and receive acknowledgements for those packets, it scales up the rate at which it sends its traffic. When a packet is dropped, the rate is reduced. In the event of tail dropping, TCP hosts will all slow their rate and start ramping it up at the same time, congesting the queue and causing tail dropping again
			- This can be prevented using Random Early Detection (RED); this is the random selection of packets to drop when the queue reaches a certain threshold (thus preventing synchronization)
				- Weighted RED allows you to control which packets are dropped depending on traffic class
## Forwarding Per-Hop Behavior
### Classification/Marking
- Organizes network traffic into classes/categories
- Fundamental to QoS in order to identify what traffic to prioritize
- Examples of classification techniques:
	- ACL
	- NBAR (Network Based Application Recognition) - looks through packets, all the way up to layer 7 to determine what kind of traffic it's looking at
	- Layer 2 & 3 Headers
- The PCP (Priority Code Point) field of the 802.1Q tag can be used to ID high/low priority traffic (only works if there *is* a dot1q tag)
- The DSCP (Differentiated Services Code Point) field of the IP header can also be used to ID high/low priority traffic
#### PCP/CoS
Ethernet Header:
![[Ethernet Header.png]]

802.1Q Tag Format:
![[802.1Q Tag Format.png]]

- 3 bits

| PCP Value | Traffic Type                                                                                       |
| --------- | -------------------------------------------------------------------------------------------------- |
| 0         | Best effort (default) = does its best to deliver it, but no guarantees it'll actually be delivered |
| 1         | Background                                                                                         |
| 2         | Excellent effort                                                                                   |
| 3         | Critical applications                                                                              |
| 4         | Video                                                                                              |
| 5         | Voice                                                                                              |
| 6         | Internetwork control                                                                               |
| 7         | Network control                                                                                    |
- Due to the requirement of an 802.1Q header, this can only be used over:
	- trunk links
	- access links with a voice VLAN
- Traffic that doesn't use one of the above links can't be prioritized with PCP (think traffic between routers outside of the internal network where the VLANs exist)
#### IP ToS Bytes:
- In the [[0. IPv4 Header|IPv4 Header]], the [[0. IPv4 Header#DSCP (Differentiated Services Code Point)|DSCP]] and [[0. IPv4 Header#ECN (Explicit Congestion Notification)|ECN]] fields combine to form the IP ToS byte (6 bits in DSCP, 2 in ECN)
- Standard markings:
##### Default Forwarding (DF) & Expedited Forwarding (EF):
- DF = best effort
	- DSCP marking is 0 (000000)
- EF = low loss/latency/jitter traffic (usually VoIP)
	- DSCP marking is 46 (101110)
##### Assured Forwarding (AF)
- A set of 12 standard values
- Defines 4 traffic classes; all packets in a class have the same priority
- Within each class, there are 3 levels of drop precedence
	- Higher drop precedence = more likely to drop the packet
	![[Assured Forwarding DSCP.png]]
	- X = base 10 of the 3 most significant bits
	- Y = base 10 of the 2 drop precedence bits
	- AFXY = DSCP Z where Z is the base 10 value of the whole DSCP field (= 8X + 2Y)
	- Greater X = Higher priority
	- Higher Y = Higher drop precedence (more like to be dropped)
##### Class Selector (CS)
- A set of 8 standard values that provides backward compatibility with IPP (IP Precedence)
- 3 most significant bits of DSCP are variable, 3 least significant bits of DSCP are set to 0

| IPP                | 0   | 1   | 2   | 3   | 4   | 5   | 6   | 7   |
| ------------------ | --- | --- | --- | --- | --- | --- | --- | --- |
| **CS**             | **CS0** | **CS1** | **CS2** | **CS3** | **CS4** | **CS5** | **CS6** | **CS7** |
| **DSCP (base 10)** | **0**   | **8**   | **16**  | **24**  | **32**  | **40**  | **48**  | **56**  |
#### RFC 4954 Recommendations:
- Voice traffic: EF
- Interactive video: AF4x
- Streaming video: AF3x
- High priority data: AF2x
- Best effort: DF

#### Trust Boundaries:
- **Trust Boundary** = where devices trust/don't trust the QoS markings of received messages
- If the markings are trusted, the device will forward the traffic without changing the markings
	- If not, the device will change the markings according to its configured policy
- It's recommended to set the trust boundary to the IP phones (configured on their internal switch)
	- Ensures the VoIP traffic from the phones will still be handled as high priority, but a tech savvy user can't set their PC's traffic to be a higher priority
### Queuing/Congestion Management
- QoS uses multiple queues
	- Different queues are created for different kinds of device traffic
- However, because only one frame can be sent out of an interface at a time, a scheduler is used to decide which queue traffic is forwarded from next
	- Prioritization allows certain queues to be prioritized over others
Traffic is received ==> Routing is done to determine the interface to send the traffic out of ==> The traffic is classified ==> The traffic enters the appropriate queue ==> The scheduler selects the appropriate queue to forward from ==> The traffic is transmitted
- Common scheduling method: weighted round-robin (packets are taken cyclically, but more data is taken from higher priority queues when the scheduler reaches them)
- **CBWFQ (Class-Based Weighted Fair Queuing)**: uses a weighted round-robin scheduler while guaranteeing each queue a certain percentage of the interface's bandwidth during congestion
- Round-robin is not ideal for VoIP and video traffic
- **LLQ (Low Latency Queuing)**: designates one (or more) queues as strict priority queues
	- This means that if there is traffic in the queue, the scheduler will always take the next packet from that queue until it is empty
	- Effective in reducing delay and jitter for VoIP/video traffic
	- However, other queues can potentially be starved by the scheduler
		- Can be solved by policing
### Shaping/Policing
- Shaping & Policing are methods to control the rate of traffic
- **Shaping** buffers traffic in a queue if the traffic rate goes over the configured rate
- **Policing** drops traffic is the traffic rate goes over the configured rate (can also remark packets instead of dropping them)
	- "Burst" traffic over the configured rate is allowed for a sort period of time
	- This accommodates data applications which typically are "bursty" in nature; instead of a constant stream of data, they send data in bursts
	- The amount of burst traffic allowed is configurable
- In both classes, classification can be used to allow for different rates for different kids of traffic
	![[Shaping & Policing Diagram.png]]
