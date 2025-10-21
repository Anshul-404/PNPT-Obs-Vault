# Filtering
---
- Ffuf provides the option to match or filter out a specific HTTP code, response size, or amount of words. 
- ![[Pasted image 20251020030123.png]]
- We know the response **size of the incorrect results**, which, as **seen from the test above, is `900`,** **and we can filter it out with `-fs 900`**. Now, let's repeat the same previous command, add the above flag, and see what we get:
	- ` ffuf -w /opt/useful/seclists/Discovery/DNS/subdomains-top1million-5000.txt:FUZZ -u http://academy.htb:PORT/ -H 'Host: FUZZ.academy.htb' -fs 900`