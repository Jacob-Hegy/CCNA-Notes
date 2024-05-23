# Configuration Management Tools
- Configuration Drift: when individual changes over time cause a device's config to deviate from the standard/correct configurations as defined by the company
	- Present potential troubleshooting issues or vulnerabilities
- Configuration Provisioning: how configuration changes are applied to devices
	- This includes configuring new devices too
- Manually configuring every devices in a network one-by-one is not practical for large networks
	- Configuration management tools like Ansible, Puppet, and Chef allow changes to be made at scale in a fraction of the required time for manual configuration
- Two essential components for configuration management tools:
	1. Template
	```
	hostname {{hostname}}
	!
	interface GigabitEthernet0/0
	ip address {{address}} {{mask}}
	ip ospf {{process_id}} area {{area}}
	```
	2. Variables
	```
	---
	hostname: R1
	address: 192.168.1.1
	mask: 255.255.255.0
	process_id: 1
	area: 0
	```
- By combining the template and variables, a config can be generated
	```
	hostname R1
	!
	interface GigabitEthernet0/0
	ip address 192.168.1.1 255.255.255.0
	ip ospf 1 area 0
	```
### Configuration Management Tools
- Tools that facilitate the centralized control of large numbers of network devices
- Can perform tasks such as:
	- Generate configurations for new devices on a large scale
	- Perform configuration changes on devices (all devices in your network, or a certain subset of devices)
	- Check device configurations for compliance with defined standards
	- Compare configurations between devices, and between different versions of configurations on the same device
# Ansible
- Owned by Red Hat; written in Python
- Agentless; no agents run on managed devices
	- Uses SSH to connect to devices, make configuration changes, extract information, etc.
- Uses a push model
	- The Ansible Server (called the Control Node) uses SSH to connect to managed devices and push configuration changes to them
	- Puppet and Chef use a pull model
- After installing Ansible, several text files must be created:
	- **Playbooks**: the "blueprints of automation tasks"; outline the logic and actions of the tasks to be performed
		- Written in YAML
	- **Inventory**: list the devices that will be managed as well as characteristics of each device such as their role (access switch, core switch, WAN router, firewall, etc.)
		- Written in INI, YAML, and other formats
	- **Templates**: represent a device's configuration file, but specific values for variables are not provided
		- Written in Jinja2 format
	- **Variables**: list variables and their values; these values are substituted into the templates to create complete configuration files
		- Written in YAML

![[Ansible Diagram.png]]
# Puppet
- Written in Ruby
- Agent-based; specific software must be installed on the managed devices
	- Not all Cisco devices support a Puppet agent
- Can be run agentless in which a proxy agent runs on an external host and the agent uses SSH to connect to the managed devices and communicate with them
- The Puppet server is called the "Puppet master"
- Uses a pull model (clients "pull" configurations from the Puppet master)
	- Clients use **TCP 8140** to communicate with the Puppet master
- Uses a proprietary language for its files instead of YAML
- Required text files on the Puppet master:
	- **Manifest**: defines the desired configuration state of a network device
	- **Templates**: similar to Ansible templates; used to generate Manifests

![[Puppet Diagram.png]]
# Chef
- Written in Ruby
- Agent-based; just like Puppet
- Uses a pull model
- Server uses **TCP 10002** to send configs to clients
- Files use a DSL (Domain-Specific Language) based on Ruby
- Required text files:
	- **Resources**: the "ingredients" in a recipe; configuration objected managed by chef
	- **Recipes**: the "recipes" in a cookbook; outline the logic and actions of the tasks performed on the resources
	- **Cookbooks**: a set of related recipes grouped together
	- **Run-list**: an ordered list of recipes that are run to bring a device to the desired configuration state

![[Chef Diagram.png]]

|                                | Ansible       | Puppet                     | Chef                 |
| ------------------------------ | ------------- | -------------------------- | -------------------- |
| **Key Files defining actions** | Playbook      | Manifest                   | Recipe, Run-list     |
| **Communication Protocol**     | SSH           | HTTPS (via REST API)       | HTTPS (via REST API) |
| **Key Port**                   | 22 (SSH port) | TCP 8140                   | TCP 10002            |
| **Agent-based/Agentless**      | Agentless     | Agent-based (or Agentless) | Agent-based          |
| **Push/Pull**                  | Push          | Pull                       | Pull                 |
