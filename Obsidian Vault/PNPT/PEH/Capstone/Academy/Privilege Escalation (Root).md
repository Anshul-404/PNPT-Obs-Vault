- We found `backup.sh` script by [[Grimmie User]]
- We can try to modify this script and try to gain root access.
- For example, let's copy `bash` binary in our own directory, give it `SUID` perms and execute it with `bash -p` to maintain `root` perms.
- ![[Pasted image 20250325065221.png]]
- After waiting sometime, we can see the `bash` binary with `suid` perms:
![[Pasted image 20250325065531.png]]
- Got `root` shell with `./bash -p`
	![[Pasted image 20250325065612.png]]
- Flag:
 ![[Pasted image 20250325065645.png]]