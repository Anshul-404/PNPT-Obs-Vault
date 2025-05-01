# Enumeration
---
- Using `linpeas`, we can see that **we are in `docker`group:**
	- ![[Pasted image 20250501165157.png]]
- That means **==we can run docker commands from inside the docker container==**
- This means, we can easily **mount the whole file system and `chroot`into it**

# Exploitation
---
- From GTFOBINS:
	- ![[Pasted image 20250501165301.png]]
- But since we don't have `alpine`image, let's see if we have any other images using `docker images`
	- ![[Pasted image 20250501165416.png]]
- Run the same command but with `bash`as image:
	- `sudo docker run -v /:/mnt --rm -it bash chroot /mnt sh`
- ![[Pasted image 20250501165502.png]]