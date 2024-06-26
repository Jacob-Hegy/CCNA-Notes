# Overview
- UDP 161 = SNMP Agent
- UDP 162 = SNMP Manager
- Two kinds of SNMP devices:
	- Managed devices: devices being managed through SNMP (ex. routers and switches)
	- Network Management Station (NMS): the device(s) managing the managed devices (SNMP server)
- Three main operations:
	- Managed devices can notify the NMS of events
	- The NMS can also inquire managed devices about info on their current status
	- The NMS can tell managed devices to change their config
- On clients, the info SNMP checks is stored inside the Management Information Base (MIB)
	- Inside the MIB are Object IDs (OIDs) that contain an identifier indicating the value of the given variable
# Versions
- SNMPv1: the original
- SNMPv2c: allows NMS to retrieve larger amounts of info; doesn't use passwords
- SNMPv3: supports strong encryption and authentication (use it)
# Messages
| Message Class | Description                                                                 | Messages                                                                                                                      |
| ------------- | --------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| Read          | Messages sent by the NMS to read info from the managed devices              | Get (request OID from agent)<br>GetNext (request info on available MIB variables)<br>GetBulk (more efficient GetNext)<br><br> |
| Write<br>     | Messages sent by the NMS to change info on the managed devices              | Set (change configured value on device)                                                                                       |
| Notification  | Messages sent by the managed devices to alert the NMS of a particular event | Trap (inform manager of something; response not sent to agent, thus unreliable)<br>Inform (trap but receives a response)<br>  |
| Response      | Messages sent in response to a previous message/request                     | Response (take a guess)                                                                                                       |
# SNMPv2c Configuration
- Optionally set info for the SNMP server with `(config)# snmp-server contact <email address>` and `(config)# snmp-server location <location name>`
- Configure the SNMPv2c community strings (passwords) with `(config)# snmp-server community <password> {ro | rw}` where ro = read only and rw = read/write
	- Default ro string is "public" and default rw string is "prviate"
	- ro means the NMS can only read info from the agent
	- rw means the NMS can read info and make changes to the device
- Specify the NMS, version, and community with `(config)# snmp-server host <NMS IP address> version <version number> <community name>`
- Configure the trap types to send to the NMS with `(config)# snmp-server enable traps <trigger>`
