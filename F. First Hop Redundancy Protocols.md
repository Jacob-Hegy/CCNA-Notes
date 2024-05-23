Purpose: Allow for router redundancy within a network by allowing a given router to act as a backup for the default gateway; if the default gateway goes down, the backup router takes its address automatically ^a11bcf
- Do this by configuring computers to use a Virtual IP (VIP) as their Default Gateway (a virtual MAC is created for the VIP)
- A given router is set as the active router and the other is standby; they use hello messages to verify that the active router is still up
- If the standby router fails to receive hello messages, it takes over as the active router and sends out gratuitous ARP messages so that switches will update their MAC address tables

# Protocols

| First Hop Redundancy Protocol             | Terminology                                                                                                                                                       | Multicast IP                     | Virtual MAC                                                                                  | Cisco Proprietary? | Load Balancing                |
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------- | -------------------------------------------------------------------------------------------- | ------------------ | ----------------------------- |
| HSRP (Hot Standby Router Protocol)        | Active/Standby                                                                                                                                                    | v1: 224.0.0.2<br>v2: 224.0.0.102 | v1: 0000.0c07.acXX<br>v2: 0000.0c9f.fXXX<br>*Where XX \|\| XXX is the HSRP group number*<br> | Yes                | Across multiple VLANs/subnets |
| VRRP (Virtual Router Redundancy Protocol) | Master/Backup                                                                                                                                                     | 224.0.0.18                       | 0000.5e00.01XX                                                                               | No                 | Across multiple VLANs/subnets |
| GLBP (Gateway Load Balancing Protocol)    | AVG (Active Virtual Gateway)/AVF (Active Virtual Forwarders; assigned by the AVG)<br><br>AVF acts as the default gateway for a portion of the hosts in the subnet | 224.0.0.102                      | 0007.b400.XXYY<br>(XX = GLBP group number, YY = AVF number)                                  | Yes                | Within a single subnet        |
# HSRP
- Uses an active/standby router configuration
	- When dealing with multiple VLANs, you can configure a different active router in each subnet/VLAN to load balance
- The active router handles the traffic sent on its VLAN while the standby(s) wait and make sure the active is still up. If the active goes down at any time, the next standby router in order will take over as the active router. This new active remains the active even when the previous active comes back online (unless configured to preempt)
	- Active router priority is decided by:
		1. Highest priority (default = 100)
		2. Highest IP address
# Configuration
- Set an interface to HSRP version 2 with `(config-if)# standby version 2`
- Configure an interface's standby group number with `(config-if)# standby <group #>`
- Configure the virtual HSRP IP address of the interface with `(config-if)# standby <group #> ip <IP address>`
- Configure the priority of the interface with `(config-if)# standby <group #> priority <0-255>`
- Configure if the interface will preemptively take the role of active router from another router with `(config-if)# standby <group #> preempt`
- View the current HSRP configuration `R# show standby`
