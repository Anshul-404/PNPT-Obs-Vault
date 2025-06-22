- Best way (as from PNPT) is to just **do all the checks**:
	- `nmap -p- -A -T4 10.10.10.10 -oN nmap/nmap_output`
- We can also grab banners **(new from CPTS)**:
	- Banner grabbing is a useful technique **to fingerprint a service quickly.**
		- `nc -nv 10.129.42.253 21`
		- `nmap -sV --script=banner -p21 10.10.10.0/24.`
		- `curl -IL https://www.inlanefreight.com`
# SMB
---
- `nmap --script smb-os-discovery.nse -p445 10.10.10.40`
- `nmap -A -p445 10.129.42.253`

- Shares access:
	- ` smbclient -N -L \\\\10.129.42.253`
		- `-N`: Suppresses password prompt

# SNMP
---
- SNMP Community strings provide information and statistics about a router or device, helping us gain access to it. 
- The manufacturer default community **strings of public and private** are often unchanged. 
- In SNMP versions 1 and 2c, **access is controlled using a plaintext community string, and if ==we know the name, we can gain access to it.**==
	
	- `snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0`
	- `snmpwalk -v 2c -c private  10.129.42.253`

- A tool such as [onesixtyone](https://github.com/trailofbits/onesixtyone) can be used to **brute force the community string names** using a dictionary file of common community strings such as the `dict.txt` file included in the GitHub repo for the tool.
	- `onesixtyone -c dict.txt 10.129.42.254`


# Practical
---
=> Perform an Nmap scan of the target. What does **Nmap display as the version of the service running on port 8080?**
- `nmap 10.129.9.33 -sV -p8080 -T4 -oN nmap/nmap_8080`
- Some bug? It never worked

=> Perform an Nmap scan of the target and identify the non-default port that the telnet service is running on.
- `rustscan -a 10.129.9.33`

=> List the SMB shares available on the target host. Connect to the available share as the bob user. Once connected, access the folder called 'flag' and submit the contents of the flag.txt file. 
- `dceece590f3284c3866305eb2473d099`
