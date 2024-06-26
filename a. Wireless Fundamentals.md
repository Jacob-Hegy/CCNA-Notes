# Access Points

# Nonoverlapping Wi-Fi Channels

# SSID
# Wireless LAN Fundamentals
- Issues:
	- All devices within range of the network receive all frames (Privacy go *womp womp*)
	- **CSMA/CA** (Carrier Sense Multiple Access with Collision Avoidance) is used to facilitate half-duplex communications and avoid collisions
		```mermaid
		graph TD;
		id1((Assemble Frame))-->id2((Is Channel Free?));
		id2((Is Channel Free?))-->|0|id3((Wait for random period of time));
		id2((Is Channel Free?))-->|1|id4((Transmit Frame));
		id3((Wait for random period of time))-->|Waited|id2((Is Channel Free?));
		```
	- Wireless communications are regulated by various international and national bodies (this applies to radio frequency ranges that are permitted for use)
	- Wireless signal coverage area must be considered
		- Signal range is affected by...
			- **Signal Absorption**: happens when the signal passes through a material and loses energy to heat, weakening the original signal
			- **Signal Refraction**: happens when a wave is bent when entering a medium where the signal travels at a different speed (ex. water)
			- **Signal Diffraction**: happens when a wave encounters an object and travels around it (causing a blindspot behind the object)
			- **Signal Scattering**: happens when a material causes a signal to scatter in all directions (dust, smog, uneven surfaces, etc. can cause scattering)
	- Other devices using the same channels can cause interference
# Radio Frequency (RF)
- Wireless signals are transmitted through radio waves that are sent via electromagnetic fields generated via alternating currents sent to an antenna
### Radio Frequency Bands
- Wi-Fi uses two main bands:
	<small>Memorizing the actual range is unnecessary for the CCNA</small>
	1. **2.4 GHz band**
		- Actual range is 2.400 GHz to 2.4835 GHz
	2. **5 GHz band**
		- Actual range is 5.150 GHz to 5.825 GHz
			- This range is divided into four smaller bands: 5.150-5.250 GHz, 5.250-5.350 GHz, 5.470-5.725 GHz, and 5.725-5.825 GHz
- 2.4 GHz band provides a larger range in open spaces and better penetration through obstacles at the cost of speed
	- However because more devices use the 2.4 GHz band than the 5 GHz band, interference can be a larger problem
- Recently Wi-Fi 6 (802.11ax) has add a new band: the 6 GHz band
### Band Channels
- Devices are configured to transmit and receive traffic on one (or more) of the channels within the bands
- The 2.4 GHz band is divided into several channels, each with a 22 MHz range
	- These channels do overlap, meaning poorly configured wireless channels will interfere with each other in a wireless network
- **It's recommended to use channels 1, 6, and 11 in the 2.4 GHz band** (these channels don't overlap)
![[2.4 GHz Band Channels.png]]

This enables the creation of a "honeycomb" pattern that provides complete coverage of an area without interference between channels
![[2.4 GHz Honeycomb Configuration.png]]

- The 5 GHz band consists of non-overlapping channels, so it's far easier to avoid interference between adjacent APs
# 802.11 Standards
| Standard | Frequencies      | Max Data Rate (theoretical) | Alternate Name |
| -------- | ---------------- | --------------------------- | -------------- |
| 802.11   | 2.4 GHz          | 2 Mbps                      |                |
| 802.11b  | 2.4 GHz          | 11 Mbps                     |                |
| 802.11a  | 5 GHz            | 54 Mbps                     |                |
| 802.11g  | 2.4 GHz          | 54 Mbps                     |                |
| 802.11n  | 2.4 or 5 GHz     | 600 Mbps                    | Wi-Fi 4        |
| 802.11ac | 5 GHz            | 6.93 Gbps                   | Wi-Fi 5        |
| 802.11ax | 2.4, 5, or 6 GHz | 4 \* 802.11ac               | Wi-Fi 6        |
# Service Sets
- Service set = group of wireless network devices
- Three main types:
	1. Independent
	2. Infrastructure
	3. Mesh
- All devices in a service set share the same SSID (service set identifier)
	- SSID = human-readable identifier for a service set
	- SSID doesn't have to be unique, but it'd be convenient if it were
### IBSS (Independent Basic Service Set)
- A wireless network in which two or more wireless devices connect directly without using an AP
	- Ex. AirDrop
- **Ad-hoc network**
- Not scalable beyond a few devices
### BSS (Basic Service Set)
- An infrastructure service set in which clients connect to each other via an AP, but not direct to each other
- A BSSID (Basic Service Set ID) is used to uniquely identify the AP
	- Other APs can use the same SSID, but not the same BSSID
	- The BSSID is the MAC address of the AP's radio
- Wireless devices request to associate with the BSS
	- Associated devices are called "clients" or "stations"
- BSA (Basic Service Area) = area around the AP where its signal is usable
### ESS (Extended Service Set)
- Used to create larger wireless LANs beyond the range of a single AP
- APs with their own BSSs are connected by a wired network
	- Each BSS uses the same SSID
	- Each BSS has a unique BSSID
	- Each BSS uses a different channel to avoid interference
- Client can pass between APs without having to reconnect, providing a seamless Wi-Fi experience when moving between APs
	- Called "roaming" (**PMKID YOU FUCKER**)
### MBSS (Mesh Basic Service Set)
- Used in situations where its difficult to run an Ethernet connection to every AP
- Uses two radios: one to provide a BSS to wireless clients and one to form a "backhaul" network which is used to bridge traffic from AP to AP
- At least one AP is connected to the wired network (this AP is called the RAP, the Root Access Point)
- A protocol is used to determine the best path through the mesh
# Distribution System
- In 802.11, the upstream wired network is called the DS (Distribution System)
- Each wireless BSS or ESS is mapped to a VLAN in the wired network
![[Distribution System Example.png]]

- It's possible for an AP to provide multiple wireless LANs, each with a unique SSID
- Each WLAN is mapped to a separate VLAN and connected to a wired network via a trunk
- Each WLAN uses a unique BSSID, usually by incrementing the last digit of the BSSID by one
![[Multiple WLAN DS.png]]

# Additional AP Operational Modes
- Repeater Mode: can be used to extend the range of a BSS by repeating signals sent by other APs in the WLAN
	- This clogs up the channel somewhat because the repeater will repeat what the AP is saying back to it within the same range
		- This is fixed by a repeater having two radios, allowing it to receive on one channel and retransmit on another
- Workgroup Bridge: operates as a wireless client of another AP and can be used to connect wired devices to the wireless network
	- Two kinds:
		- Universal WGB (uWGB): 802.11 standard
		- WGB: Cisco-proprietary version
- Outdoor Bridge: can be used to connect networks over long distances without a physical cable connecting them
	- APs will use specialized antennas that focus most of the signal power in one direction, which allows connection over a much longer distance
	- Can be used to make point-to-point connections or even Hub-Spoke networks
