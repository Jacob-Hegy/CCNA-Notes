# Data Serialization
- The process of converting data into a standardized format/structure that can be stored (in a file) or transmitted (over a network) and reconstructed later (i.e. by a different application)
	- This allows the data to be communicated in a format that's easily understood by both applications
- Data serialization languages allow us to represent variables with text
# JSON
- Standardized in RFC 8259
- Often used in REST API
- Whitespace is insignificant
- Four primitive data types:
	1. String
		- A text value surrounded by double quotes (ex. `"Hello"`)
	2. Number
		- A numeric value (ex. `5`)
	3. Boolean
		- Either true or false (ex. `true`)
	4. Null
		- The intentional absence of any object value (`null`)
- Two structured data types:
	1. Object/Dictionary
		- An unordered list of key-value pairs surrounded by curly brackets
		- The key is a string and the value is any valid JSON data type
		- The key and value are separated by a colon, and multiple key-value pairs are separated by commas
		- Ex.
		```json
		{"interface_name": "GigabitEthernet1/1",
		"is_up": true,
		"ip_address": "192.168.1.1",
		"netmask": "255.255.255.0",
		"speed": 1000}
		```
		OR
		```json
		{
			"device": {
				"name": "R1",
				"vendor": "Cisco",
				"model": "1101"
			},

			"interface_config": {
				"interface_name": "GigabitEthernet1/1",
				"is_up": true,
				"ip_address": "192.168.1.1",
				"netmask": "255.255.255.0",
				"speed": 1000
			}
		}
		```
	2. Array
		- Series of values separated by commas within square brackets
			- Values don't have to be the same data type
		```json
		{
			"interfaces": [
				"GigabitEthernet1/1",
				"GigabitEthernet1/2",
				"GigabitEthernet1/3"
			],

			"random_values": [
				"Hi",
				5
			]
		}
		```
# XML (Extensible Markup Language)
- Markup language used as a data serialization language
- Generally less human-readable than JSON
- Whitespace is insignificant
- Often used by REST APIs
- Format: `<key>value</key>`
	- Ex. `<Interface>GigabitEthernet0/0</Interface>`
- Output in XML format in the Cisco IOS with `R# <show command> | format`
# YAML (YAML Ain't Markup Language)
- Originally *Yet Another Markup Language*, but later became *YAML Ain't Markup Language* to distinguish itself as a data serialization language
- **Used by [[45. Ansible, Puppet, & Chef|Ansible]]**
- Very human-readable
- Whitespace **is significant**
	- Indentation is very important
- YAML files start with `---`
	- `-` is used to indicate a list
	- Key-value pairs are represented as `<key>:<value>`
- Ex.
```YAML
---
ip_interfaces:
- Interface: GigabitEthernet0/0
  IP-Address: 192.168.1.1
  OK?: 'YES'
  Method: manual
  Status: up
  Protocol: up
- Interface: GigabitEthernet0/1
  IP-Address: unassigned
  OK?: 'YES'
  Method: unset
  Status: administratively down
  Protocol: down
```
