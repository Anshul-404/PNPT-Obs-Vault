- Let's assume our client has asked us to **test their AD environment** from a **managed host with no internet access**, and **all efforts to load tools onto it have failed.**
- Our client wants to see what types of enumeration are possible, **so we'll have to resort to "living off the land"** or only **using tools and commands native to Windows/Active Directory.**
- This can also be a **more stealthy approach** and may not create as many log entries and alerts as pulling tools into the network in previous sections.
# Env Commands For Host & Network Recon
---
- ![[Pasted image 20250808070454.png]]
## Basic Enumeration
- `Systeminfo`: The systeminfo command will print a summary of the **host's information for us** in one tidy output. 

# Harnessing PowerShell
--- 
- It is a **powerful scripting language** and can be **used to dig deep into systems.** 
- PowerShell has **many built-in functions and modules** we can use on an engagement to **recon the host and network and send and receive files.**
	- ![[Pasted image 20250808071019.png]]

# Downgrade Powershell
---
- Many **defenders are unaware that several versions of PowerShell often exist on a host.**
- If not uninstalled, they can still be used.
- Powershell event **logging was introduced as a feature with Powershell 3.0** and forward. **With that in mind, we can attempt to call Powershell version 2.0 or older**
- r. If successful, **==our actions from the shell will not be logged in Event Viewer.==**

	- `powershell.exe -version 2`
	- `Get-host`
	- `get-module`
		- ![[Pasted image 20250808071158.png]]
## Checking Defenses
---
- Firewall Checks:
	- `netsh advfirewall show allprofiles`
- Windows Defender Check (from CMD.exe):
	- `sc query windefend`
- Below we will check the status and configuration settings with the Get-MpComputerStatus cmdlet in PowerShell:
	- `Get-MpComputerStatus`

# Am I Alone?
---
- When landing on a host for the first time, one important thing is to check and **see if you are the only one logged in.**
- If you start taking actions from a host someone else is on, t**here is the potential for them to notice you.**

## Using qwinsta
- `qwinsta`
- ![[Pasted image 20250808071752.png]]

# Network Information
---
![[Pasted image 20250808071953.png]]
![[Pasted image 20250808072025.png]]
- Using `arp -a` and `route print` will not only benefit in **enumerating AD environments, but will also assist us in identifying opportunities to pivot to different network segments in any environment.**

# Windows Management Instrumentation (WMI)
----
- [Windows Management Instrumentation (WMI)](https://docs.microsoft.com/en-us/windows/win32/wmisdk/about-wmi) is a **scripting engine** that is widely used within **Windows enterprise environments to retrieve information and run administrative tasks on local and remote hosts.**
- ![[Pasted image 20250808072415.png]]
- This **[cheatsheet](https://gist.github.com/xorrior/67ee741af08cb1fc86511047550cdaf4) has some useful commands for querying host and domain info using wmic:**

# Net Commands
----
- [Net](https://docs.microsoft.com/en-us/windows/win32/winsock/net-exe-2) commands can be **beneficial to us when attempting to enumerate information** from the domain.
- We can list information such as:
	- Local and domain users
	- Groups
	- Hosts
	- Specific users in groups
	- Domain Controllers
	- Password requirements
- Keep in mind that **`net.exe` commands are typically monitored by EDR solutions** and can **quickly give up our location** if our assessment has an evasive component.
		![[Pasted image 20250808072653.png]]
		- ![[Pasted image 20250808072727.png]]
## Net Commands Trick
- If you believe the **network defenders are actively logging/looking for any commands** out of the normal, **you can try this workaround to using net commands:**
	- Typing **`net1` instead of `net` will execute the same functions** without the potential trigger from the net string.

# Dsquery
---
- [Dsquery](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc732952\(v=ws.11\)) is a helpful command-line tool that can be **utilized to find Active Directory objects.**
- **`dsquery` will exist on any host with the `Active Directory Domain Services Role` installed**, and the `dsquery` DLL exists on all modern Windows systems by default now and can be found at `C:\Windows\System32\dsquery.dll`.

## Dsquery DLL
- All we need is elevated privileges on a host or the ability to run an **instance of Command Prompt or PowerShell from a `SYSTEM` context.** Below, we will show the basic search function with `dsquery` and a few helpful search filters.

- User Search:
	- `dsquery user`
	- ![[Pasted image 20250808073005.png]]
- Computer Search:
	- `dsquery computer`

- Wildcard Search:
	- `dsquery * "CN=Users,DC=INLANEFREIGHT,DC=LOCAL"`

- We can, of course, **combine dsquery with LDAP search filters of our choosing.** 
- The **below looks for users with the PASSWD_NOTREQD flag set in the userAccountControl attribute.**:
	- `squery * -filter "(&(objectCategory=person)(objectClass=user)(userAccountControl:1.2.840.113556.1.4.803:=32))" -attr distinguishedName userAccountControl`

- Searching for Domain Controllers:
	- `dsquery * -filter "(userAccountControl:1.2.840.113556.1.4.803:=8192)" -limit 5 -attr sAMAccountName`

# LABS
---
- Utilizing techniques learned in this section, find the flag hidden in the description field of a disabled account with administrative privileges. Submit the flag as the answer.
	- `Search-ADAccount -AccountDisabled -UsersOnly | Select-Object SamAccountName, DistinguishedName`
		- This gives disabled accounts
	- `net user bross /domain`