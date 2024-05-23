# 802.11 Frame Format
<small>At least be familiar with the format and be able to recall it</small>
![[802.11 Frame.png]]

- Frame Control: provides info such as message type and subtype
- Duration/ID: has various purposes
	- Time in ms the channel will be dedicated for transmission of the frame
	- ID for the association
- Addresses 1-4 (which addresses are present depends on the type of message)
	1. Destination Address (DA): final recipient of the frame
	2. Source Address (SA): original sender of the frame
	3. Receiver Address (RA): immediate recipient of the frame
	4. Transmitter Address (TA): immediate sender of the frame (not necessarily the original sender)
- Sequence Control: used to reassemble fragments and delete dupes
- [[27. QoS|QoS]] Control: used for traffic prioritization
- HT (High Throughput) Control: enables high throughput operations (added in 802.11n)
	- 802.11n = High Throughput (HT) Wi-Fi
	- 802.11ac = Very High Throughput (VHT) Wi-Fi
- Frame Body: payload
- FCS (Frame Check Sequence): checksum used to check for errors
# 802.11 Association Process
- Three connection states:
	- Not authenticated, not associated
	- Authenticated, not associated
	- Authenticated and associated
- Either actively scan by sending probe requests and listen for a probe response from the AP or listen for beacon messages from an AP
- Probe Request (Supplicant) ==> Probe Response (AP) ==> Authentication Request (Supplicant) ==> Authentication Response (AP) ==> Association Request (Supplicant) ==> Association Response (AP)
# 802.11 Message Types
- Three types:
	1. **Management**: used to manage the BSS
		- Beacon
		- Probe request/response
		- Authentication
		- Association request/response
	2. **Control**: used to control access to the medium (radio frequency); assists with the delivery of management and data frames
		- RTS (Request to Send)
		- CTS (Clear to Send)
		- ACK
	3. **Data**: the actual data being transmitted
# Wireless AP Architectures
### Autonomous
- Self-contained systems that don't rely on a WLC
- Configured individually via a console cable, Telnet/SSH, or web portal
	- Thus, optimal for smaller networks
- IP address for remote management should be configured
- RF parameters must be manually configured (transmit power, channel, etc.)
- Security policies are handled individually by each AP
- QoS rules etc. are configured individually on each AP
![[Autonomous AP Configuration Example.png]]
- APs connect to the wired network with a trunk link ^e0f51b
- Separate management traffic and data traffic into separate VLANs (this leads to VLANs spanning the entire network, which is bad practice due to large broadcast domains, spanning tree disabling links, and the setup being labor intensive)
- Can function in Repeater, Outdoor Bridge, and Workgroup Bridge mode
### Lightweight APs/Split-MAC Architecture
- Functions can be split between the AP and a Wireless LAN Controller (WLC)
	- All traffic is sent to the WLC before being sent to its destination from there
- Handle "real-time" operations like transmitting/receiving RF traffic, encryption/decryption, sending out beacons/probes, etc. (essentially edge computing)
	- Other operations, such as RF management, security, QoS, authentication, and association, are sent to the WLC
- This is called **split-MAC architecture**
- The WLC is also used to centrally configure the lightweight APs
	- The WLC and the lightweight APs authenticate each other using X.509 digital certificates installed on each device
	- Use CAPWAP (Control and Provisioning of Wireless Access Points) to communicate
- Two tunnels are created between each AP and the WLC:
	- The **control tunnel (UDP 5246)** is used to configure the APs and control/manage the operations; all traffic is encrypted
	- The **data tunnel (UDP 5247)** is used to send data from the APs to the WLC; this does not enter the wired network directly and is not encrypted by default (however, it can be encrypted using DTLS/Datagram Transport Layer Security)
- APs connect to switch *access ports*, not trunk ports, the send CAPWAP data to the WLC
	- The WLC uses a trunk port to connect to the wired network
![[Lightweight AP Example.png]] ^0cdedd
- Benefits:
	- Scalability: very easy to build out and support thousands of APs
	- Dynamic Channel Assignment: the WLC can automatically select which channel each AP should use
	- Transmit Power Optimization: the WLC can automatically set the appropriate transmit power for each AP
	- Self-healing Wireless Coverage: when an AP stops functioning, the WLC can increase the transmit power of nearby APs to avoid coverage holes
	- Seamless Roaming: clients can roam between APs with no noticeable delay
	- Client Load Balancing: when the client is in range of two APs, the WLC can associate the client with the lesser-used AP to balance the load among APs
	- Security/QoS Management: central management of security and QoS policies ensures consistency across the network
- Modes:
<small>Understand the basic purpose of each</small>
	- **Local**: default mode; AP offers 1 or more BSSs for the client to associate with
	- **FlexConnect**: offers 1 or more BSSs like local, but also allows the AP to locally switch traffic between the wired and wireless networks if the tunnels to the WLC go down
	- **Sniffer**: the AP doesn't offer a BSS for clients; instead, it captures 802.11 frames and forwards them to a device running a network sniffer
	- **Monitor**: the AP doesn't offer a BSS for clients; instead, it receives 802.11 frames to monitor for rogue devices and dissociate them if found
	- **Rogue Detector**: the AP doesn't use its radio; instead, it listens to traffic on the wired network and receives a list of suspected rogue clients and AP MAC addresses from the WLC. Using this list, the AP can detect rogue devices
	- **SE-Connect (Spectrum Expert Connect)**: the AP doesn't offer a BSS for clients; instead, it performs RF spectrum analysis on all channels. This info can be forwarded to devices running software that can collect and analyze the data
	- **Bridge/Mesh**: similar to Outdoor Bridge; the AP can act as a dedicated bridge between sites
	- **Flex plus Bridge**: adds FlexConnect functionality to Bridge/Mesh; allows WAPs to locally forward traffic even if connectivity to the WLC is lost
### Cloud-based APs
- In-between autonomous and split-MAC architecture
	- Autonomous APs that are centrally managed in the cloud
- **Cisco Meraki is a popular cloud-based Wi-Fi solution**
	- The Meraki dashboard can be used to configure APs, monitor the network, generate performance reports, tell the APs which channel to use, what transmit power to use, etc.
- Only management/control traffic is sent to the cloud; data traffic is sent directly to the wired network like in Autonomous AP architecture
![[Cloud-Based AP Example.png]]
# Wireless LAN Controllers (WLC) Deployments
- WLCs are used in [[38. Wireless Architecture#Lightweight APs/Split-MAC Architecture|split-MAC architecture]]
- Four main deployment models
### Unified
- The WLC is a hardware appliance in a central location of the network
![[Unified WLC Example.png]]
- Can support up to ~6000 APs
	- Additional WLCs can be added to support more APs
- Good for large, enterprise networks
### Cloud-based
- The WLC is a VM running on a server (usually in a private cloud)
		- This is **not** the same as cloud-based AP architecture
![[Cloud-Based WLC Example.png]]
- Can support ~3000 APs
	- Additional WLC VMs can be deployed to support more APs
### Embedded
- The WLC is integrated within an AP
![[Embedded WLC Example.png]]
- Can support ~200 APs
	- More switches with embedded WLCs can be added to support more APs
- Good for small networks
### Mobility Express
- The WLC is integrated within an AP
![[Mobility Express WLC Example.png]]
- Can support ~100 APs
	- More APs with embedded Mobility Express WLCs can be added to support more APs
- Suitable for a small branch office
