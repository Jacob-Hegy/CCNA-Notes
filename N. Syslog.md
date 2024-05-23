# Overview
- Standard protocol for message logging
	- Logs events that take place on a device (ex. changes in interface status, changes in [[14. OSPF|OSPF]] neighbor status, system restarts, etc.)
- Can be displaying in the CLI, saved in the RAM, or sent to an external server
# Message Format
`seq:time stamp: %facility-severity-MNEMONIC:description`
`seq` = Sequence number indicating order of messages
`time stamp` = timestamp indicating the time the message was generated
`facility` = which process generated the message
`severity` = a number indicating the severity of the event
`MNEMONIC` = a short code for the message, indicating what happened
`description` = detailed info about the event being reported
ex. `000043 Feb 11 03:02:55.304 %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up`
`seq` = `000043`
`time stamp` = `Feb 11 03:02:55.304`
`facility` = `LINK`
`severity` = `3` (Critical)
`MNEMONIC` = `UPDOWN`
`description` = `Interface GigabitEthernet0/0, changed state to up`
# Facilities and Severity Levels
| Level | Keyword       | Description                                         |
| ----- | ------------- | --------------------------------------------------- |
| 0     | Emergency     | System is unusable                                  |
| 1     | Alert         | Action must be taken immediately                    |
| 2     | Critical      | Critical conditions                                 |
| 3     | Error         | Error conditions                                    |
| 4     | Warning       | Warning conditions                                  |
| 5     | Notice        | Normal but significant condition (**Notification**) |
| 6     | Informational | Informational messages                              |
| 7     | Debugging     | Debug-level messages                                |
<font size=5>E</font>very <font size=5>A</font>wesome <font size=5>C</font>isco <font size=5>E</font>ngineer <font size=5>W</font>ill <font size=5>N</font>eed <font size=5>I</font>ce cream <font size=5>D</font>aily
## Logging Locations
- Console line: messages displayed in the CLI of the device (all messages are displayed by default)
- VTY (Virtual TeletYpe) lines: messages will be displayed in the CLI when connected to a device via [[24. SSH|Telnet/SSH]]; disabled by default
- Buffer: messages will be saved to RAM; by default, all messages are displayed
	- Can view the messages with `show logging`
- External server: messages can be configured to be forwarded to an external server
	- Syslog servers will listen for messages on **UDP 514**
# Configuration
- Configure logging to the command line with `(config)# logging console {<level> | <keyword>}`
	- Configures logging for this level and all above this level; thus, specifying level 6 would log all messages from levels 0 to 6 would be logged
- Configure logging to the VTY lines with `(config)# logging monitor {<level> | <keyword>}`
	- Even after configuring this, Syslog messages will still not be displayed by default; **every time** you connect via Telnet or SSH, use `R# terminal monitor`
- Configure logging to the buffer with `(config)# logging buffered [size] {<level> | <keyword>}`
	- Buffer size is in bytes
- Configure logging to an external server with `(config)# logging [host] <IP address>`
	- Configure the level of messages sent to the external server with `(config)# logging trap {<level> | <keyword>}`
- Prevent syslog messages from interrupting input in the CLI with `(config)# line console 0` then `(config-line)# logging synchronous`
- Enable timestamps in the Syslog messages with `(config)# service timestamps log {datatime | uptime}`
- Enable sequence numbers in the Syslog messages with `(config)# service sequence-numbers`
