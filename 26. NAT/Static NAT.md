Recall private IPv4 address ranges (RFC 1918):
- 10.0.0.0/8
- 172.16.0.0/12
- 192.168.0.0/16
- Subnet as you please
# Intro
- NAT is used to modify the source and/or destination IP addresses of packets
	- This is done to allow hosts with private IP address to communicate with one another over the internet
- We'll be working with *Source NAT*
# What is Static NAT?
- Involves statically configuring 1-to-1 mappings of private IP addresses to public IP addresses
- An *inside local* IP is mapped to an *inside global* IP
	- Inside Local = the IP address of the internal host from the perspective of the local network
	- Inside Global = the IP address of the internal host from the perspective of outside hosts (IP of the host *after NAT*)
	- Outside Local = the IP address of the external host from the perspective of the local network
	- Outside Global = the IP address of the external host from the perspective of the outside network
	- Outside Local and Outside Global are always the same unless destination NAT is used
- Ex. Host 192.168.0.167 wants to communicate with 8.8.8.8. When its packets reach the router, the source IP address is translated from 192.168.0.167 to 100.0.0.1 (this is a public IP address mapped 1-to-1 with the original IP address). The packets are then sent to the server who sends its response to 100.0.0.1 (this goes to the router because, from the server's perspective it believes it is communicating with 100.0.0.1 directly). The response packets then have their destination IP address translated back and sent to 192.168.0.167
- Each internal host must map to their own inside global IP address (1-to-1 mapping)
# Configuration
- Define the "inside" interface(s) connected to the internal network with `(config-if)# ip nat inside`
- Define the "outside" interface(s) connected to the external network with `(config-if)# ip nat outside`
- Configure a one-to-one IP address mapping with `(config)# ip nat inside source static <inside local IP> <global local IP>`
- View all configured NAT translations with `R# show ip nat translations`
- Clear all dynamic translations in the NAT translation table with `R# clear ip nat translation *`
- View NAT stats with `R# show ip nat statistics`
