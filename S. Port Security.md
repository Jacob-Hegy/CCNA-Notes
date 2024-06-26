# What and Why
- Security feature on Cisco switches to control with MAC addresses are allowed to enter a switchport
	- If a disallowed MAC address interacts with the port, some action is taken (default sets the interface into an "err-disabled" state)
- By default, only one MAC address is allowed (if not configured, the first MAC address to enter the interface is set as the default)
	- The max number of MAC addresses can be configured
- Allows network admins to control which devices are allowed to access the network
	- MAC spoofing can easily circumvent this though
	- Limiting the number of MAC addresses on an interface is usually a better fix for this compared to specifying MAC addresses
# Violation Modes
- There are three different modes that determine what the switch will do if an unauthorized MAC address enters a port security enabled interface
	1. Shutdown
		- Shuts down the port, placing it in err-disabled
		- Generates a Syslog and/or SNMP message
		- Violation counter is set to 1
	2. Restrict
		- The switch discards traffic from unauthorized MAC addresses
		- The interface is NOT disabled
		- Generates a Syslog and/or SNMP message every time an unauthorized MAC address is detected
		- Increments the violation counter by 1 for every unauthorized frame
	3. Protect
		- Traffic from unauthorized MAC addresses is discarded
		- The interface is NOT disabled
		- No Syslog or SNMP message is generated
		- Violation counter not incremented
# Secure MAC Address Aging
- By default, secure MAC address won't "age-out" (expire after a certain amount of time)
- Aging can be configured with `(config-if)# switchport port-security aging time <minutes>`
- Two types of aging:
	1. Absolute: default; once a MAC address is learned, a timer decrements until it reaches zero and removes the MAC address (even if it continues receiving traffic)
	2. Inactivity: same as absolute except the timer resets every time traffic from the source's MAC address is received
- MAC addresses statically configured for port-security don't age-out by default
# Sticky Secure MAC Addresses
- A sticky MAC address never ages-out, even if static aging is configured
- Once configured, sticky secure must be saved to the startup-config
- When configured, all dynamically-learned secure MAC addresses will be converted to sticky secure MAC addresses (and vice versa when sticky secure is disabled)
# Configuration
- Enable port security on an interface with `(config-if)# switchport port-security`
	- Port security can be enabled on access ports or trunk ports, but only if they're statically configured. Auto and desirable ports cannot be enabled
- Configure the maximum amount of MAC addresses with `(config-if)# switchport port-security maximum <x>`
- View the port security configuration on an interface with `S# show port-security interface <int>`
- View the port security status of all interfaces with `S# show port-security`
- To reenable an interface, shut it down then turn it back on
- Alternatively, a disabled interface can automatically come back online if configured with ErrDisable Recovery using `(config)# errdisable recovery cause <cause>` (for port security, the cause would be `psecure-violation`)
	- View ErrDisable Recovery configuration with `S# show errdisable recovery`
	- Set the recovery interval with `(config)# errdisable recovery interval <seconds>`
- Manually allow a MAC address with `(config-if)# switchport port-security mac-address <MAC address>`
- Enable restrict mode with `(config-if)# switchport port-security violation restrict`
- Enable protect mode with `(config-if)# switchport port-security violation protect`
- Enable shutdown mode with `(config-if)# switchport port-security violation shutdown`
- Configure the aging type with `(config-if)# switchport port-security aging type {absolute | inactivity}`
- Enable the aging-out of statically configured MAC addresses with `(config-if)# switchport port-security aging static`
- Enable sticky secure MAC addresses with `(config-if)# switchport port-security mac-address sticky`
- View all secure MAC addresses with `S# show mac address-table secure`
