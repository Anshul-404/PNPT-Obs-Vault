- Use **firefox containers** so we can use **multiple users.**
	- ![[Pasted image 20250406100447.png]]
- ![[Pasted image 20250406100610.png]]
- ![[Pasted image 20250406100655.png]]


# Exploitation
---
- In first container:
	 ![[Pasted image 20250406100855.png]]- ![[Pasted image 20250406100841.png]]
- In second container, we can see this comment:
	- ![[Pasted image 20250406100938.png]]


- In First container:
	- `<script> prompt(1) </script>`
		- ![[Pasted image 20250406101030.png]]
- In Second container (after refresh):
	- ![[Pasted image 20250406101049.png]]


# Grabbing Cookie
---
- Set a cookie on second container:
	- ![[Pasted image 20250406101323.png]]
- On First container:
	- `<script>alert(document.cookie)</script>`
	- ![[Pasted image 20250406101410.png]]
- On Second container again:
	- ![[Pasted image 20250406101445.png]]