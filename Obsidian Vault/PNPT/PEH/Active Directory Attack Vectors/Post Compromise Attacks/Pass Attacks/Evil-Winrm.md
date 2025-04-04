- Can be used for **connecting to windows using only the NT hash** or **password**
- Useful **when `crackmapexec` and `secretsdump` do not work** as they require whole NTLM hash.
- Has **`upload/download`** options after connection
- Starts in **`powershell`**

- `evil-winrm -i <ip> -u <user> -H <NT_Hash>`
- `evil-winrm -i <ip> -u <user> -p <password>`
	![[Pasted image 20250404170151.png]]