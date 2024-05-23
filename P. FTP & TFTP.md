# FTP vs TFTP
- TFTP only allows basic operations (reading and writing files)
	- Also uses "lock-step" communication, meaning the server sends a message and then waits to receive an ACK before sending the next one
- TFTP Connections:
	1. Connection: client sends request to the server & server responds back
	2. Data Transfer: client & server exchange TFTP messages (server sends data, client sends ACKs)
	3. Connection Termination: last data message is sent and a final ACK is sent to terminate the connection
- FTP uses TCP 21 to send FTP commands and replies & TCP 20 sends and receives the actual data
- FTP Active mode = server establishes FTP data (TCP 20) connection
- FTP Passive mode = client establishes FTP data connection (useful when client is behind a firewall)
# IOS File Systems
- View file systems of a Cisco IOS device with `R# show file systems`
	- `disk` = storage devices such as flash memory
	- `opaque` = storage used for internal functions
	- `nvram` = internal NVRAM; `startup-config` is stored here
	- `network` = external file systems, like FTP/TFTP servers
# Using FTP/TFTP in Cisco IOS
- View current IOS version with `R# show version`
- View the contents of flash storage with `R# show flash`
- Copy a file using FTP/TFTP use `R# copy <src> <dest>`
	- Ex. `R# copy tftp: flash:`
	- Ex. `R# copy ftp: flash:`
- Change IOS version to new one saved on flash with `(config)# bot system <filepath>`
- Reload the device with `R# reload`
- To delete a file use `R# delete <filepath>`
- Configure the username and password used for an FTP server with `(config)# ip <server name> username <username>` and `(config)# ip <server name> password <password>`
