# PrivEsc Checklists
---
- One excellent resource is **[HackTricks](https://book.hacktricks.xyz)**, which has an excellent checklist for both **[Linux](https://book.hacktricks.wiki/en/linux-hardening/linux-privilege-escalation-checklist.html) and [Windows](https://book.hacktricks.wiki/en/windows-hardening/checklist-windows-privilege-escalation.html) local privilege escalation**. 
- Another excellent repository is **[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)**, which also has checklists for both [Linux](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md) and [Windows](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)

# Enumeration Scripts
---
- Some of the **common Linux enumeration scripts include [LinEnum](https://github.com/rebootuser/LinEnum.git) and [linuxprivchecker](https://github.com/sleventyeleven/linuxprivchecker),** and for Windows include **[Seatbelt](https://github.com/GhostPack/Seatbelt) and [JAWS](https://github.com/411Hall/JAWS).**
- Another useful tool we may use for server enumeration is the **[Privilege Escalation Awesome Scripts SUITE (PEASS)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite)**, as it is well maintained to remain up to date and includes scripts for enumerating both Linux and Windows.

# Kernel Exploits
----
- **An old operating system**: Suppose the server is not being maintained with the latest updates and patches. In that case, it is likely vulnerable to specific kernel exploits found on unpatched versions of Linux and Windows.
- For example, the **above script showed us the Linux version to be `3.9.0-73-generic`**. If we Google exploits for this version or use `searchsploit`, we would find a `CVE-2016-5195`, otherwise known as `DirtyCow`. **We can search for and download** the [DirtyCow](https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs) exploit and run it on the server to gain root access. 

# Vulnerable Software
---
- Another thing we should look for is installed software. 
- ==For example, **we can use the `dpkg -l` command on Linux** or look at **`C:\Program Files` in Windows** to see what software is installed on the system.==

# User Privileges
---
- Below are some common ways to exploit certain user privileges:
    Sudo
    SUID
    Windows Token Privileges
- `sudo -l`

# Scheduled Tasks
----
There are usually two ways to take advantage of **scheduled tasks (Windows)** or **cron jobs (Linux)** to escalate our privileges:
1. ==**Add new scheduled tasks/cron jobs**==
2. ==**Trick them to execute a malicious software**==

There are specific directories that we may be able to utilize to add new cron jobs if we have the `write` permissions over them. These include:

1. `/etc/crontab`
2. `/etc/cron.d`
3. `/var/spool/cron/crontabs/root`

# Exposed Credentials
---
- This is very common with ==**`configuration` files, `log` files,== and user history files (==`bash_history`== in Linux and ==`PSReadLine`== in Windows).**
- We may also check for **==`Password Reuse`==,** as the system user may have **==used their password for the databases,==** which may allow us to use the same password to switch to that user.

# SSH Keys
---
- If we have read access over the .ssh directory for a specific user, we may read their private ssh keys found in **==/home/user/.ssh/id_rsa or /root/.ssh/id_rsa,==** and use it to log in to the server. 

- If we find ourselves with **==write access to a users`/.ssh/` directory==**, we can place our ==**public key** in the user's ssh directory at `**/home/user/.ssh/authorized_keys**`.==
	- We must first **create a new key on our machine with `ssh-keygen` and the `-f` flag** to specify the output file:
	  
```
DrAsstrange@htb[/htb]$ ssh-keygen -f key

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase): *******
Enter same passphrase again: *******

Your identification has been saved in key
Your public key has been saved in key.pub
The key fingerprint is:
SHA256:...SNIP... user@parrot
The key's randomart image is:
+---[RSA 3072]----+
|   ..o.++.+      |
...SNIP...
|     . ..oo+.    |
+----[SHA256]-----+
```

- This will give us two files: `key` (which we will use with `ssh -i`) and `key.pub`, which we will copy to the remote machine.
- ==Let us copy `key.pub`, then on the remote machine, we will add it into== `/root/.ssh/authorized_keys`:
	- `user@remotehost$ echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys`

- **==As we can see, we can now ssh in as the user `root`.==**