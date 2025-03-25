- From nmap scan:
	- ```21/tcp open  ftp     vsftpd 3.0.3
	| ftp-anon: Anonymous FTP login allowed (FTP code 230)
	|_-rw-r--r--    1 1000     1000          776 May 30  2021 note.txt```
	
- **FTP Enumeration**
---
- Version: `vsftpd 3.0.3`
	- Only exploit found here was [DOS](https://www.exploit-db.com/exploits/49719), so not useful for us.
- Exploring the `note.txt` shared file as anonymous
![[Pasted image 20250325050701.png]]
# note.txt
---
- Information Disclosure:
```
Hello Heath !
Grimmie has setup the test website for the new academy.
I told him not to use the same password everywhere, he will change it ASAP.
I couldn't create a user via the admin panel, so instead I inserted directly into the database with the following command:
INSERT INTO `students` (`StudentRegno`, `studentPhoto`, `password`, `studentName`, `pincode`, `session`, `department`, `semester`, `cgpa`, `creationdate`, `updationDate`) VALUES
('10201321', '', 'cd73502828457d15655bbd7a63fb0bc8', 'Rum Ham', '777777', '', '', '', '7.60', '2021-05-29 14:36:56', '');
The StudentRegno number is what you use for login.
Le me know what you think of this open-source project, it's from 2020 so it should be secure... right ?
We can always adapt it to our needs.
-jdelta
```
- Possible credentials:
	- `10201321 : cd73502828457d15655bbd7a63fb0bc8 : Rum Ham`
- Cracking with hashcat:
	- `hashcat -a 0 hash --wordlist /usr/share/wordlists/john.lst -m 0`
![[Pasted image 20250325054408.png]]
- `10201321 : cd73502828457d15655bbd7a63fb0bc8 : student`