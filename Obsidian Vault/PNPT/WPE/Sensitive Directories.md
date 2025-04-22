## 📁 **User-Specific Directories**
---

These are per-user and often contain **browser creds, tokens, saved passwords**, config files, etc.

### 🔹 `C:\Users\<User>\AppData\Local\`
- `Google\Chrome\User Data\Default\Login Data` → Browser passwords
- `Microsoft\Credentials\` → DPAPI-protected stored credentials
- `Packages\Microsoft.Windows.Terminal_*\LocalState` → SSH keys
- `Temp\` → Apps often leave junk (installer logs, debug dumps)
- `Microsoft\Vault\` → DPAPI vault secrets
- Any app name (e.g., `WinSCP`, `PuTTY`, `RDP apps`, etc.)

### 🔹 `C:\Users\<User>\AppData\Roaming\`
- `Microsoft\Windows\Start Menu\Programs\Startup\` → Startup scripts 
- `WinSCP.ini`, `FileZilla\recentservers.xml` → Saved creds
- `Thunderbird\`, `Outlook\`, `KeePass\` → Email creds, DBs
- `Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt` → Command history

### 🔹 `C:\Users\<User>\Documents\`
- `Passwords.txt`, `creds.docx`, `.kdbx`, `.rdg`, etc.
- Custom config or key files (SSH, VPN, etc.)
    
### 🔹 `C:\Users\<User>\Downloads\`
- Leaked key files, password exports, installers with sensitive configs
    
### 🔹 `C:\Users\<User>\Desktop\`
- CTF flag, notes, temporary password files
---

## 🔐 **System/Global Directories**
---
These are common to all users, but admins or services may drop important data here.
### 🔹 `C:\Windows\System32\config\`
- `SAM`, `SYSTEM`, `SECURITY` →  Local user hashes & secrets
- Can be dumped via `secretsdump.py` if you have them
    
### 🔹 `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup\`
- Global startup scripts
- Can be abused for persistence or privilege escalation
    
### 🔹 `C:\ProgramData\`
- Common app configs
- Apps like `OpenVPN`, `MySQL`, `PostgreSQL`, `.ssh`, `.env`, etc.
- Sometimes `.ini` or `.conf` files with creds
    
### 🔹 `C:\Windows\Panther\` and `C:\Windows\System32\Sysprep\`
- `unattend.xml`, `sysprep.xml` →  May contain `AutoLogon` with plaintext admin password

### 🔹 `C:\Windows\Tasks\` or `C:\Windows\System32\Tasks\`
- Scheduled task definitions (may contain plaintext or sensitive logic)

---

## 🧩 **Other High-Value Items**
---

These aren’t folder-specific but should be searched _anywhere_ in the mounted image:

### 🔎 File Patterns to Find:
```
find /mnt/vhd -type f \( \
    -iname "*password*" -o \
    -iname "*cred*" -o \
    -iname "*.kdbx" -o \
    -iname "*.rdg" -o \
    -iname "*.ppk" -o \
    -iname "*.pem" -o \
    -iname "*.key" -o \
    -iname "*.xml" -o \
    -iname "*.config" -o \
    -iname "*.ini" -o \
    -iname "*.rdp" \
\)
```

### **🔎 Grep for keywords:**
`grep -iE 'pass|pwd|token|secret|Authorization|user|login' /mnt/vhd/**/* 2>/dev/null`


## 🧰 Tools That Help:

|Tool|Use|
|---|---|
|`secretsdump.py`|Dump SAM hashes if SYSTEM + SAM available|
|`vaultcmd` (Windows)|Interact with Vault|
|`firepwd`, `dechrome`, `creds.py`|Dump browser saved creds|
|`Jumplist Explorer`, `r3con1`|MRU and recent file usage|
|`regdump`, `registryExplorer`|Parse SYSTEM & SOFTWARE hives


### ✅ **TL;DR: Hit List**

|Location|What to Look For|
|---|---|
|`AppData\Local`|Browser creds, vaults, temp secrets|
|`AppData\Roaming`|Saved sessions, startup scripts|
|`Documents`, `Desktop`|Notes, `.rdg`, `.kdbx`, text creds|
|`ProgramData`|Configs, `.env`, shared secrets|
|`System32\Config`|SAM/SYSTEM/SECURITY|
|`Panther\`, `Sysprep\`|`unattend.xml` — admin creds|
|`*.rdp`, `*.ppk`, `*.pem`, `*.config`|Manual inspection or grep|
|Startup folders|Persistence + LPE opportunities|
