- The router dynamically maps inside local addresses to local global addresses as needed
- An ACL is used to identify which traffic should be translated
	- If the source IP is permitted by the ACL, it will be translated
	- If the source IP is denied by the ACL, it won't be translated (however, it won't be dropped)
- A NAT pool is used to define the available inside global addresses
- Mappings are still one-to-one
	- If all inside global IP addresses are used up, it's called NAT pool exhaustion
		- This results in packets from non-mapped hosts being dropped until one of the inside global IP addresses becomes available (this happens automatically with a time out or when manually cleared)
# Dynamic NAT Configuration
- Define the inside and outside interfaces the same is in done with [[Static NAT#Configuration|Static NAT]]
- Define permitted traffic using an ACL with `(config)# access-list <acl id> permit <IP Address> <Wildcard Mask>`
- Define the inside global NAT pool with `(config)# ip nat pool <pool name> <IP start range> <IP end range> {prefex-length x | netmask <subnet mask>}` ^5ce8ca
- Map the ACL to the NAT pool with `(config)# ip nat inside source list <acl id> pool <pool name>`
# PAT (NAT Overload)
- Translates both the IP address and the port number
- By using a unique port number for each internal host, a single public IP address can be used by many different internal hosts
![[PAT.png]]

# PAT Configuration
- Define the inside and outside interfaces the same as is done with [[Static NAT#Configuration|Static NAT]] and as seen [[#Dynamic NAT Configuration|above]]
- Define the ACL and NAT pool the same as seen [[#Dynamic NAT Configuration|above]]
- Map the ACL to the NAT pool and enable PAT with `(config)# ip nat inside source list <acl id> pool <pool name> overload`
- Alternatively, map the ACL to the router's interface's own IP address with `(config)# ip nat inside source list <acl id> interface <int> overload`
