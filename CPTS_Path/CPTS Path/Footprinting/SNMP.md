`Simple Network Management Protocol` ([SNMP](https://datatracker.ietf.org/doc/html/rfc1157)) was created to **monitor network devices.** In addition, this protocol can also be used to handle configuration tasks and change settings remotely.
- PORT - 161 and 162
- **MIB = Management Information Base** - Similar to AD DS SCHEMA
	- Think of a **MIB** as a **dictionary of things you can ask a device about**.
	- It defines **data objects** you can query using SNMP.
	- Each object has a **name** (like `sysName`, `ifDescr`, `cpuLoad`) and a corresponding **OID**.
	- MIBs are usually stored in files and loaded into SNMP tools to translate raw OIDs into readable names.
- **OID = Object Identifier**
	- It’s a unique numbered path to a specific data item on a networked device.
    - Hierarchical (tree-like) structure.
	- Each node in the tree represents a category or a specific object.
![[Pasted image 20250616071424.png]]
![[Pasted image 20250616071224.png]]

# Community Strings
---
- **==Community strings can be seen as passwords==** that are used to determine whether the **requested information can be viewed or not.** 
- It is important to note that many organizations are still using SNMPv2, as the transition to SNMPv3 can be very complex, but the services still need to remain active. 
- At the same time, the lack of encryption of the data sent is also a problem. Because every time the **==community strings are sent over the network, they can be intercepted and read.==**

# Default Configuration
---
- SNMP Daemon Config:  cat /etc/snmp/snmpd.conf

# Dangerous Settings
---
![[Pasted image 20250616071412.png]]

# Footprinting
---
- For footprinting SNMP, we can use tools like snmpwalk, onesixtyone, and braa. 
- **Snmpwalk is used to query the OIDs with their information.**
- Once we know the community string and the SNMP service that does not require authentication (versions 1, 2c), we can query internal system information like in the previous example.
- **Onesixtyone can be used to brute-force the names of the community strings** since they can be named arbitrarily by the administrator. 
- `snmpwalk -v2c -c public 10.129.14.128`
- `onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128`

- Often, when certain community strings are bound to specific IP addresses, they are named with the hostname of the host, and sometimes even symbols are added to these names to make them more challenging to identify. 
- 
- Once we know a community string, we can use it **with [braa](https://github.com/mteg/braa) to brute-force the individual OIDs and enumerate the information behind them.**
- ` braa <community string>@<IP>:.1.3.6.*   # Syntax`
- `braa public@10.129.14.128:.1.3.6.*`