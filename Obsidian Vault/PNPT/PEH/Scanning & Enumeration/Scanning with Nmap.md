1. **Kioptrix** (VMWare Machine):
   > john: TwoCows2
       192.168.157.128
   - Finding IP of VM using `sudo arp-scan -l`
	   - This uses `arp (Address Resolution Protocol)` to discover devices on the network.
   - `sudo netdiscover -r 192.168.1.0/24`: Can also be used
     
1. **Common Nmap Options:**
---
   - `nmap -T4 -p -A <ip> -oN <file_to_store_results>`
	   - `T (1-5)` -> Threads (or speed), usually use 4 as detection is not an issue
	   - `-p 139,443,21`-> ports to scan, `-p-` -> would indicate all the ports, otherwise top 1000 ports by default
	   - `-A` -> All types of check, Everything, Version, OS, fingerprinting, etc.
	   - `-oN` -> Store results in nmap format, other options are: `-oX, -oS, etc`
	   - `-sS` -> Stealth Scan (by default) => `SYN -> SYN/ACK -> RST`
	   - `-sU` -> UDP ports scan (use without `-p-` as it takes a lot of time)
	   - `-O` -> OS detection, `-sC` -> script scanning, `-sV` -> service detection => All included while using `-A`, might as well us that :)
	     
  -  `nmap -T4 -p- -A 192.168.157.128 -oN nmap/nmap_kioptrix_all`
  - Results: [[Nmap Scan for kioptrix]]
    ![[Pasted image 20250324132829.png]]