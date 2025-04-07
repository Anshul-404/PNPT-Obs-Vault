- Access control issue where **we can request a resource with an object ID** and it **returns some information of the object**. For example, user ids, product ids, order ids, etc
- Test for IDOR:- find a point **==where we can manipulate ID and then change it==** 

# Exploitation
---
![[Pasted image 20250407164432.png]]
- Change id to `1009`:
	- ![[Pasted image 20250407164504.png]]
- Automation using `ffuf`:
	- make a list of IDs till `2000` using python: `python3 -c "for i in range(2001): print(i)" >> id_list.txt`
	- `ffuf -u 'http://localhost/labs/e0x02.php?account=FUZZ' -w id_list.txt -fw 189`
	- ![[Pasted image 20250407164740.png]]

# Challenge - Identify Admin Accounts
---
- Copy `Get` request as `cURL`
	- ![[Pasted image 20250407164835.png]]
- Convert [curl to python](https://curlconverter.com/)
	- ![[Pasted image 20250407164931.png]]
- Script Linked [[Bruteforce Script]]
	- ![[Pasted image 20250407165605.png]]
- ![[Pasted image 20250407165844.png]]
- ![[Pasted image 20250407165854.png]]
- ![[Pasted image 20250407165904.png]]
- ![[Pasted image 20250407165917.png]]