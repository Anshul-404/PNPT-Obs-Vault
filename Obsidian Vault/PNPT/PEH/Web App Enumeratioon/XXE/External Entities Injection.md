- ==**For applications accepting and parsing `XML**`==
- ==**XML data to API endpoints that expect `json==`**
- Most of the times, **copy from [payloadallthethings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XXE%20Injection)**

- **Allows an attacker to interfere with an application's processing of XML data**. It often allows an attacker **to view files.**
- XXE vulnerabilities arise because the XML specification contains various **potentially dangerous features, and standard parsers**

# Exploitation
---
- For a structure:
	- ![[Pasted image 20250407163652.png]]
- Safe `xxe` would be:
	- ![[Pasted image 20250407163718.png]]
- Unsafe would be:
	- ![[Pasted image 20250407163742.png]]
	![[Pasted image 20250407163903.png]]