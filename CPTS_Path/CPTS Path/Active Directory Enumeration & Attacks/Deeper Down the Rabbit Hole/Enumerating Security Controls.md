- After gaining a foothold, we could use this access to get a feeling for the defensive state of the hosts, **enumerate the domain further now that our visibility is not as restricted**, and, if necessary, work at "living off the land" by using tools that exist natively on the hosts.
- Understanding the **protections we may be up against will help inform our decisions regarding tool usage and assist us in planning our course of action** by either avoiding or modifying certain tools.

# Windows Defender
---
- Windows Defender (or [Microsoft Defender](https://en.wikipedia.org/wiki/Microsoft_Defender) after the Windows 10 May 2020 Update) has greatly improved over the years and, **by default, will block tools such as `PowerView`.**
- We can use the built-in **PowerShell cmdlet [Get-MpComputerStatus](https://docs.microsoft.com/en-us/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) to get the current Defender status**. 
- Here, **==we can see that the `RealTimeProtectionEnabled` parameter is set to `True`, which means Defender is enabled==** on the system.

## Checking the Status of Defender with Get-MpComputerStatus
- `Get-MpComputerStatus`
- ![[Pasted image 20250807064851.png]]

# AppLocker
---
- An application **whitelist is a list of approved software applications** or executables that are **allowed to be present and run on a system.**
- The goal is to **protect the environment from harmful malware and unapproved software** that does not align with the specific business needs of an organization.[](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker)
- t provides granular control over executables, scripts, Windows installer files, DLLs, packaged apps, and packed app installers.
- Organizations **also often focus on blocking the `PowerShell.exe` executable,** **but forget about the other [PowerShell executable locations](https://www.powershelladmin.com/wiki/PowerShell_Executables_File_System_Locations) such as** `%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe` or `PowerShell_ISE.exe`

- All Domain Users are disallowed from running the 64-bit PowerShell executable located at:
	`%SystemRoot%\system32\WindowsPowerShell\v1.0\powershell.exe`

## Using Get-AppLockerPolicy cmdlet
- `Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections`
- ![[Pasted image 20250807065026.png]]

# PowerShell Constrained Language Mode
---
- PowerShell [Constrained Language Mode](https://devblogs.microsoft.com/powershell/powershell-constrained-language-mode/) l**ocks down many of the features needed to use PowerShell effectively, such as blocking COM objects, only allowing approved .NET types, XAML-based workflows,** PowerShell classes, and more. 
- We can quickly enumerate whether we are in Full Language Mode or Constrained Language Mode.

## Enumerating Language Mode
- `$ExecutionContext.SessionState.LanguageMode`
- ![[Pasted image 20250807065108.png]]

# LAPS
---
- The Microsoft [Local Administrator Password Solution (LAPS)](https://www.microsoft.com/en-us/download/details.aspx?id=46899) is used to **randomize and rotate local administrator passwords on Windows hosts and prevent lateral movement.**
- We can **enumerate what domain users can read the LAPS password set for machines with LAPS installed and what machines do not have LAPS installed.**
- The [LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit) greatly facilitates this with several functions.
## Using Find-LAPSDelegatedGroups
- `Find-LAPSDelegatedGroups`
- ![[Pasted image 20250807065200.png]]

- The `Find-AdmPwdExtendedRights` **checks the rights on each computer with LAPS enabled for any groups with read access and users with "All Extended Rights."**
## Using Find-AdmPwdExtendedRights
- `Find-AdmPwdExtendedRights`
- ![[Pasted image 20250807065222.png]]
- We can use the **`Get-LAPSComputers` function to search for computers that have LAPS enabled when passwords expire**, and even the randomized passwords in cleartext if our user has access.

## Using Get-LAPSComputers
- `Get-LAPSComputers`
- ![[Pasted image 20250807065250.png]]