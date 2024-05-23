# Console Port Security
- No password is needed by default to access the CLI of a Cisco IOS device via the console port
- To configure a password on the console line, use the following:
```
(config)# line console 0 (the 0 just means that there is only a single console line, so the number will always be 0)
(config-line)# password <password>
(config-line)# login (this requires the password to be used on login when accessing the CLI via the console port)
```
- Additionally, you can require users to also use one of the usernames configured on the device with the following:
```
(config)# username <username> secret <password>
(config)# line console 0
(config)# line console 0
(config-line)# login local (requires users to use one of the configured usernames on the device to login
```
- Configure auto logout due to inactivity with `(config-line)# exec-timeout <minutes> <seconds>`
# Layer 2 Switch Management IP
- An IP address can be registered to an SVI (switch virtual interface) on a layer 2 switch to allow remote connections to the CLI with Telnet or SSH
- Configure the SVI with the following:
```
(config)# interface vlan <vlan ID>
(config-if)# ip address <IP address> <Subnet Mask>
(config-if)# no shutdown
(config-if)# exit
(config)# ip default-gateway <IP address> (required for communicating with the switch outside its network)
```
# Telnet Configuration
- Configure a password and enable it with `(config)# enable secret <password>`
	- Alternatively, configure a username and password with `(config)# username <username> secret <password>`
		- Make sure you enable it with `(config-line)# login local`
- Configure an ACL to limit which devices can connect to the VTY lines with `(config)# access-list <number> permit host <IP address>`
	- Apply the ACL to the VTY lines with `(config-line)# {access-class | ip access-group} <access list number> in`
		- `access-class` applies the ACL to the VTY lines
		- `ip access-group` applies an ACL to an interface
- Configure the number of simultaneous users by setting the number of VTY lines using `(config)# line vty <starting line> <ending line>`
	- Ex. To configure 16 simultaneous lines, use `(config)# line vty 0 15`
- Enable remote connections with `(config-line)# transport input {telnet | ssh | telnet ssh | all | none`
	- `telnet` only allows Telnet
	- `ssh` only allows SSH
	- `telnet ssh` allows both Telnet and SSH
	- `all` allows all connections
	- `none` allows no connections
# SSH Configuration
***SSH Configuration is required for the CCNA***
- Note that IOS images that support SSH will have `K9` in their name when you run `SW# show version`
- Configure the FQDN (Fully Qualified Domain Name \[host name + domain name]) with `(config)# ip domain name <domain name>`
- Generate the RSA keys with `(config)# crypto key generate rsa [modulus <length>]`
	- You will then be asked how many bits you want the keys to be if you didn't use `modulus <length>`
- Check the SSH configuration with `(config)# do show ip ssh`
- Username, Password, and ACL configurations are the same as Telnet
- Optionally, set the SSH version with `(config)# ip ssh version <version number>`
- VTY line configuration is the same as Telnet
- You must configure `(config-line)# login local` to use SSH
- Applying automatic timeout, limits on the VTY line connections, and the ACL to the VTY lines is also the same as Telnet
- In summary
	1. Configure the host name (`(config)# hostname <host name>`
	2. Configure the DNS domain name (`(config)# ip domain name <domain name>`)
	3. Generate RSA key pair
	4. Configure and enable password/username & password
	5. Enable SSHv2 (only)
	6. Configure VTY lines
