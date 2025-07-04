# Common credential patterns
---
![[Pasted image 20250702055037.png]]

# Hunting from Windows
---
## Snaffler
- [Snaffler](https://github.com/SnaffCon/Snaffler) is a C# program that, when run on **a `domain-joined` machine,** automatically **==identifies accessible network shares and searches for interesting files.==**
	- `Snaffler.exe -s`
- While they assist with automation, **a fair amount of manual review is typically required,** as many matches **may turn out to be `"false positives"`.**
- Two useful **parameters that can help refine Snaffler's** search process are:
	- `-u` retrieves a list of users from Active Directory and searches for references to them in files
	- `-i` and `-n` allow you to specify which shares should be included in the search

## PowerHuntShares
- [PowerHuntShares](https://github.com/NetSPI/PowerHuntShares) is a PowerShell script that ==**doesn't necessarily need to be run on a domain-joined machine.**==
- One of its most useful features is that it **==generates an `HTML report`==** upon completion.
	- ![[Pasted image 20250702055326.png]]
- We can run a basic scan using `PowerHuntShares` like so:
	- `Set-ExecutionPolicy -Scope Process Bypass`
	- `Import-Module .\PowerHuntShares.psm1`
	- `Invoke-HuntSMBShares -Threads 100 -OutputDirectory c:\Users\Public`

# Hunting from Linux
---
- If we **don’t have access** to a domain-joined computer, or simply prefer to search for files remotely.
## MANSPIDER
- [MANSPIDER](https://github.com/blacklanternsecurity/MANSPIDER) offers many parameters that can be configured to fine-tune the search.
- It's best to run `MANSPIDER` using the official Docker container to avoid dependency issues.
- A basic scan for files **containing the string `passw`** can be run as follows:
	- `docker run --rm -v ./manspider:/root/.manspider blacklanternsecurity/manspider 10.129.234.121 -c 'passw' -u 'mendres' -p 'Inlanefreight2025!'`

## ==NetExec==
- `NetExec` can also be used to **==search through network shares using the `--spider` option.==**
- A basic scan of network shares for files containing the **string `"passw"`** can be run like so:
	- `nxc smb 10.129.234.121 -u mendres -p 'Inlanefreight2025!' --spider IT --content --pattern "passw"`



# Labs
---
- Use the credentials `mendres:Inlanefreight2025!` to connect to the target either by RDP or WinRM.
- `Snaffler` and `PowerHuntShares` can be found in `C:\Users\Public`.


=> As this user, search through the additional shares they have access to and identify the password of a domain administrator. What is it?
- Took 2 hours to find this single question. **No tool worked except for manspider on docker**:
	- `sudo docker run --rm -v ./manspider:/root/.manspider blacklanternsecurity/manspider 10.129.234.173 -c 'passw' -u 'jbader' -p 'ILovePower333###'`
	- ![[Pasted image 20250702083903.png]]